# Support environment

NicePay's API supports over `TLS 1.2`, and provides various options and test environments for developers.

- Support various development languages
- node.js, python, ruby, jsp, php, classic asp, .net
- REST API
- HTTP Status code
- Detailed response code
- Webhook
- Sandbox

<br>

## Browser support

NicePay's Hosted Payment Page was developed to be used in 'HTML5' based PC/Mobile browsers.

<br>

### Browser support(PC)

In this table, "OS not supported" means that the browser is not available for that operating system.

On a PC, the Hosted Payment Page stops Internet Explorer 8 and earlier with an error message. It does not check the operating system (last reviewed 2026-09-23).

<table>
  <thead>
    <tr>
      <th scope="col">Operating system</th>
      <th scope="col">Chrome</th>
      <th scope="col">Firefox</th>
      <th scope="col">Internet Explorer 9 or later</th>
      <th scope="col">Opera</th>
      <th scope="col">Whale</th>
      <th scope="col">Edge</th>
      <th scope="col">Safari</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Windows 7</th>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>OS not supported</td>
      <td>OS not supported</td>
    </tr>
    <tr>
      <th scope="row">Windows 8</th>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>OS not supported</td>
      <td>OS not supported</td>
    </tr>
    <tr>
      <th scope="row">Windows 10</th>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>OS not supported</td>
    </tr>
    <tr>
      <th scope="row">Windows 11</th>
      <td>Yes</td>
      <td>Yes</td>
      <td>OS not supported</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>OS not supported</td>
    </tr>
    <tr>
      <th scope="row">Mac-OSX</th>
      <td>Yes</td>
      <td>Yes</td>
      <td>OS not supported</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
    </tr>
  </tbody>
</table>

<br>

### Browser support(Mobile)

NAVER and Daum are Korean web portal apps, BAND is a group messenger from NAVER, and KakaoTalk is a messenger from Kakao.

| Company  |  type   |   App   |  support  |
|:---------|:-----:|:--------:|:------:|
| naver    | Browser  |  naver   |   Yes    |
| daum     | Browser  |   daum   |   Yes    |
| google   | Browser  |  google  |   Yes    |
| telegram |  messenger  |   telegram   |   Yes    |
| naver    |  messenger  |    band    |   Yes    |
| naver    |  messenger  |   line   |   Yes    |
| kakao    |  messenger  |   kakaotalk   |   Yes    |
| facebook |  SNS  |   facebook   |   Yes    |

This manual does not confirm support for Safari on iOS, Chrome on Android, or a WebView in your own app. [Open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) before you rely on them. If your app opens the Hosted Payment Page in a WebView, see `appScheme` in [Only Mobile App option](../api/nicepay-api-payment-window-url.md#only-mobile-app-option).

<br>

## Amounts and currencies

Every amount in a request is a whole number: `amount`, `taxFreeAmt`, `cancelAmt`, and the `amount` of Check Authorization Amount. Send it as a JSON number with no decimal point, for example `1004`. NicePay does not reject a decimal number in a JSON body: it drops the fraction, so `10.50` becomes `10`.

| Currency | Unit of `amount` |
|:---|:---|
| `KRW` | Korean won, with no decimal places. `1004` is KRW 1,004. |
| `USD` | Not confirmed in this manual, see below |
| `CNY` | Not confirmed in this manual, see below |

NicePay sends the number in `amount` to the payment network as it is and does not convert it for the currency. This manual does not confirm whether a `USD` or `CNY` amount is in the main unit (dollars, yuan) or in the smallest unit (cents, fen). It also does not confirm in which currency a response shows the `amount` of such a payment. [Open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) to confirm both before you send a payment in `USD` or `CNY`.

- **Accepted values**: `currency` takes `KRW`, `USD` or `CNY`, in upper case. Another value fails with [`U132`](../code/nicepay-code.md#api-response-code).
- **Checkout** (`POST /v1/checkout`): when you leave `currency` out, the payment is in `KRW`.
- **Key-in** (`POST /v1/key-in/payments`): `currency` has no default. When you leave it out, the response `currency` is `null`. Send `KRW` for a payment in won.
- **Recurring Payment**: `KRW` only. The request has no `currency` field, and the response `currency` is always `KRW`.
- **Cancel and Check Authorization Amount**: the request has no `currency` field. NicePay checks `cancelAmt` and the `amount` to check against the amount that it stores for the payment, which is the `amount` in its responses. Write them in the same unit as that `amount`.

### Tax breakdown

`taxFreeAmt` is the tax-free part of `amount`, in the same unit. It must not be greater than `amount`: Checkout rejects a larger value with [`U327`](../code/nicepay-code.md#api-response-code), and Key-in and Recurring Payment reject it with [`U321`](../code/nicepay-code.md#api-response-code).

NicePay computes the rest of the tax breakdown from `amount` and `taxFreeAmt` (0 when you leave it out). It uses the Korean VAT rate of 10% for every currency:

- `supplyAmt` = (`amount` - `taxFreeAmt`) / 1.1, rounded half up to a whole number
- `goodsVat` = `amount` - `taxFreeAmt` - `supplyAmt`
- `serviceAmt` = 0

For example, an `amount` of `1004` with no `taxFreeAmt` gives `supplyAmt` `913`, `goodsVat` `91` and `serviceAmt` `0`.

Checkout has no `supplyAmt`, `goodsVat` or `serviceAmt` request field. Key-in, Recurring Payment and Cancel ignore these three fields when you send them.

For a partial cancellation, NicePay applies the same formula to `cancelAmt` and the `taxFreeAmt` of the cancel request, with two exceptions:

- When the cancellation takes all of the taxable amount that is left, NicePay uses the `supplyAmt` and `goodsVat` that are left on the payment.
- When the formula gives a `goodsVat` greater than the VAT that is left, NicePay uses the VAT that is left and adds the difference to `supplyAmt`.

### Accepting overseas customers

- **Payment methods for `USD` and `CNY`**: NicePay's API does not limit `currency` by payment method. The payment network decides, and it can reject the payment with [`A145`, `A146` or `A248`](../code/nicepay-code.md#api-response-code). This manual does not list the payment methods that accept `USD` or `CNY`. [Open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) to confirm them for your merchant account.
- **`currency` in responses**: only a payment by card or by easy-pay wallet can return a `currency` other than `KRW`. Virtual account, bank transfer and mobile phone payments always return `KRW`.
- **Overseas-issued cards**: NicePay's API does not check which country issued a card. The payment network returns [`3051`](../code/nicepay-code.md#api-response-code) when your merchant account is not registered for overseas cards, and [`3053`](../code/nicepay-code.md#api-response-code) when it cannot verify an overseas card. [Open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) to confirm whether your merchant account accepts overseas-issued cards.
- **Key-in and Recurring Payment**: with authentication type `10` or `11`, `encData` carries the card holder's date of birth (`idNo`), and with type `03` or `11` the first 2 digits of the card password (`cardPw`). See [encData Field Details](../api/nicepay-api-keyin.md#encdata-field-details). This manual does not confirm whether the payment network approves an overseas-issued card with these types.
- **Sandbox**: you cannot test `USD` or `CNY` in Sandbox. The Create checkout response shows the `currency` that you sent, but the payment result (`returnUrl` callback) and Transaction Status Inquiry always return `KRW`.

<br>

## Dates and times

### Dates in responses

NicePay returns dates and times in Korea Standard Time (KST, UTC+9), in this pattern:

```bash
Pattern : yyyy-MM-dd'T'HH:mm:ss.SSSZ
Example : 2023-03-24T14:04:16.982+0900
```

This applies to `ediDate`, `paidAt`, `failedAt`, `cancelledAt`, `expireDate`, `vbankExpDate`, `authDate`, `expireAt` and `eventToDate` in API responses, in the `returnUrl` callback and in webhooks. Exception: the Create checkout response returns `vbankExpDate` as NicePay stored it, `yyyyMMdd` or `yyyyMMddHHmm` (for example `20230325` or `202303252359`).

- The offset is `+0900`, with no colon, and the milliseconds always have 3 digits.
- `paidAt`, `failedAt` and `cancelledAt` are the text `0` when the event has not happened, for example `failedAt` of a successful payment. Check for `0` before you parse the value.
- Use `ediDate` exactly as received when you verify `signature`. Do not parse it and write it again: `+09:00`, UTC or another number of milliseconds gives a different hash.

To parse a value:

- Java: `OffsetDateTime.parse(value, DateTimeFormatter.ofPattern("yyyy-MM-dd'T'HH:mm:ss.SSSZ"))`. `OffsetDateTime.parse(value)` without the formatter fails, because it expects `+09:00`.
- Python: `datetime.strptime(value, "%Y-%m-%dT%H:%M:%S.%f%z")`
- JavaScript: add a colon to the offset first, for example `new Date(value.replace(/([+-]\d{2})(\d{2})$/, "$1:$2"))`. The JavaScript date format requires `+09:00`, and not every engine accepts `+0900`.

### Dates in requests

- **`ediDate`**: a timestamp that your Merchant Server creates for each request, for example `2023-03-24T16:55:00.000+0900`. NicePay uses it only as text inside `signData` and does not parse it, so its time zone does not change the result. Send `ediDate` whenever you send `signData`, and use exactly the same text in the request and in the `signData` hash. Write `signData` in lowercase hexadecimal, because some APIs compare it case-sensitively.
- **Query strings**: in a `GET` request, send `ediDate` and `signData` as query parameters, and percent-encode them. The `+` in `+0900` must become `%2B`. An unencoded `+` reaches NicePay as a space, and the `signData` check then fails with [`U312`](../code/nicepay-code.md#api-response-code). See the example in [Transaction Status Inquiry (with tid:Transaction ID)](../api/nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id).
- **`expireDate`** (Checkout): send UTC with `Z`, for example `2023-03-25T04:58:01Z`, or the response pattern with milliseconds and an offset without a colon, for example `2023-03-25T13:58:01.000+0900`. Other forms, such as an offset with a colon (`+09:00`) or no offset, fail with [`U322`](../code/nicepay-code.md#api-response-code).
- **`vbankExpDate`** (Checkout, `method` `vbank`): send a date and a time in KST, as `yyyy-MM-ddTHH:mm`, for example `2023-03-25T23:59`. NicePay also accepts a date only (`2023-03-25`), but it then stores only the date, and this manual does not confirm the closing time on that day. A value in one of the `expireDate` forms also works. Other values fail with [`U330`](../code/nicepay-code.md#api-response-code).

<br>