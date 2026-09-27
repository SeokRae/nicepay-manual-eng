# Cancel

## Cancel / Refund

### Over-view
You can use this API to cancel and refund transactions for which payment (approval) has been completed.  
Card payment will be canceled by sending `POST` data with tid (Transaction ID) to the cancel API `/v1/payments/{tid}/cancel`.  
For a Checkout payment, you can send the `sessionId` to `/v1/payments/checkout/{sessionId}/cancel` instead, for example when you did not store the `tid`. Both paths cancel the same payment in the same way. The `signData` of both is built from the `tid`, so to send `signData` you need the `tid` either way.  
To refund a virtual account payment after the customer has deposited the money, also send the refund account: `refundAccount`, `refundBankCode` and `refundHolder`.  
To cancel a payment whose result your server could not confirm, use [Net cancel](#net-cancel).  

<br>

<a href="../image/payment-checkout-cancel-cycle.svg"><img alt="Sequence diagram: order, create checkout, redirect, pay, result page, browser posts to returnUrl, status inquiry, confirm order; Cancellation: cancel, result" src="../image/payment-checkout-cancel-cycle.svg" width="800px"></a>

**Steps 11-12 above (bracketed as Cancellation)** are this server-to-server call the merchant makes after a card or easy-pay checkout has already completed (see [Card and Easy Pay checkout flow](./nicepay-api-payment-window-url.md#card-and-easy-pay-checkout-flow) for steps 1-10); there's no customer/browser step here. `refundAccount`/`refundBankCode`/`refundHolder` only apply to virtual account refunds and are omitted above; see the parameter tables below for the full field list.

<br>

**Before you start**, you'll need:
- A [Client and Secret key](../info/nicepay-info-key.md) issued from the NicePay admin console
- An `Authorization` header built from those keys, see [Basic and Bearer authentication](../info/nicepay-info-basic-token.md)
- We recommend testing against the [Sandbox](../info/nicepay-info-sandbox.md) first, then switching to Live once verified. The example below calls Sandbox (`sandbox-api.nicepay.co.kr`) with the public Sandbox key from [Test key information](../info/nicepay-info-sandbox.md#test-key-information). For Live, use `api.nicepay.co.kr` and your own Live key. Sandbox takes full cancellations only.

<br>

### Cancel with session id example

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/payments/checkout/641d555b91ae1/cancel' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
--data '{
    "reason" : "sample-code",
    "orderId" : "merchant-order-id"
}'
```

<br>

### Cancel / Refund period by Payment method

| Payment method        | Cancel All | Partial cancellation | Full Refund | Partial Refund | Cancellation period |
|:----------------------|:----------:|:--------------------:|:-----------:|:-------------:|:-------------------:|
| Credit card payment   | Yes        | Yes                  | No          | No             | within 1 year       |
| Virtual account (after issuance) | Yes | No                 | No          | No             | Before deposit expiration |
| Virtual account (after deposit) | No | No                   | Yes         | Yes            | 180 days            |
| Cash receipt          | Not via this API | Not via this API | No          | No             | 1 year after issued |
| Escrow (before registration for shipping) | Yes | No        | No          | No             | -                   |
| Escrow (after shipping registration) | Yes | No             | No          | No             | -                   |
| Escrow (after purchasing decision) | No | No                | No          | No             | -                   |
| Escrow (after purchase rejection) | Yes | No                | Yes         | No             | -                   |

> Cancelling a cash-receipt payment through `/v1/payments/{tid}/cancel` or `/v1/payments/checkout/{sessionId}/cancel` fails with [`U106`](../code/nicepay-code.md#api-response-code) ("cash receipt cancellation requires a separate API"). That separate cash-receipt cancellation API is not part of the API this manual documents; [open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) if you need it.  

<br>

> **⚠️ Important:** If the virtual account refund request is successful, it will be refunded as of D+1 17:00 on the business day.

<br><br>

### Cancel Request parameter (with sessionId)

```bash
POST /v1/payments/checkout/{sessionId}/cancel  
HTTP/1.1  
Host: api.nicepay.co.kr  
Authorization: Basic <credentials>  or Bearer <token>  
Content-type: application/json;charset=utf-8  
```

Required: Yes = always send; No = optional; Conditional = send in the case stated in Description. Bytes = maximum length in bytes.

| Parameter     | Type      | Required | Bytes | Description |
|:--------------|:---------:|:--------:|:------:|:-----------|
| `reason`         | String    | Yes | 100   | Cancellation reason |
| `orderId`        | String    | Yes | 64    | Order ID for this cancel request. NicePay finds the payment by the `sessionId` in the path, not by this value.<br>For a partial cancellation, send a value you have never used, not the `orderId` of the payment: a used value fails with [`U112`](../code/nicepay-code.md#api-response-code). |
| `cancelAmt`      | Int       | No          | 12    | Cancellation amount<br>Whole number with no decimal point, in the same unit as the `amount` that NicePay returns for the payment. See [Amounts and currencies](../info/nicepay-info-general.md#amounts-and-currencies)<br>If the value is missing, full cancellation will be occured<br>For a partial cancellation, the value must not exceed the amount not yet cancelled, or the request fails with [`U123`](../code/nicepay-code.md#api-response-code)<br>In Sandbox, a request that contains `cancelAmt` always fails, even when the value equals the full amount. The error is [`U128`](../code/nicepay-code.md#api-response-code) unless an earlier check fails first. Leave `cancelAmt` out to cancel in Sandbox |
| `mallReserved`   | String    | No          | 500   | Spare field for store information delivery |
| `ediDate`        | String    | Conditional | -     | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `signData`       | String    | No          | 256   | Forgery Verification Data<br>Rule: hex(sha256(tid + ediDate + SecretKey))<br>Uses the `tid` of the payment, even on this `sessionId` path |
| `returnCharSet`  | String    | No          | 10    | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |
| `taxFreeAmt`     | Int       | No          | 12    | Tax-free part of the cancellation amount, a whole number with no decimal point<br>For a partial cancellation, the value must not exceed the tax-free amount not yet cancelled, or the request fails with [`U319`](../code/nicepay-code.md#api-response-code) |
| `refundAccount`  | String    | Conditional | 16    | Refund account number<br>Required when you cancel a virtual account payment after the customer has deposited the money (a refund) |
| `refundBankCode` | String    | Conditional | 3     | Bank code of the refund account, see [Bank code](../code/nicepay-code.md#bank-code)<br>Required when you cancel a virtual account payment after the customer has deposited the money (a refund) |
| `refundHolder`   | String    | Conditional | 10    | Holder name of the refund account<br>Required when you cancel a virtual account payment after the customer has deposited the money (a refund) |

<br><br>

### Cancel Request parameter (with tid)

```bash
POST /v1/payments/{tid}/cancel  
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter     | Type      | Required | Bytes | Description |
|:--------------|:---------:|:--------:|:------:|:-----------|
| `reason`         | String    | Yes | 100   | Cancellation reason |
| `orderId`        | String    | Yes | 64    | Order ID for this cancel request. NicePay finds the payment by the `tid` in the path, not by this value.<br>For a partial cancellation, send a value you have never used, not the `orderId` of the payment: a used value fails with [`U112`](../code/nicepay-code.md#api-response-code). |
| `cancelAmt`      | Int       | No          | 12    | Cancellation amount<br>Whole number with no decimal point, in the same unit as the `amount` that NicePay returns for the payment. See [Amounts and currencies](../info/nicepay-info-general.md#amounts-and-currencies)<br>If the value is missing, full cancellation will be occured<br>For a partial cancellation, the value must not exceed the amount not yet cancelled, or the request fails with [`U123`](../code/nicepay-code.md#api-response-code)<br>In Sandbox, a request that contains `cancelAmt` always fails, even when the value equals the full amount. The error is [`U128`](../code/nicepay-code.md#api-response-code) unless an earlier check fails first. Leave `cancelAmt` out to cancel in Sandbox |
| `mallReserved`   | String    | No          | 500   | Spare field for store information delivery |
| `ediDate`        | String    | Conditional | -     | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `signData`       | String    | No          | 256   | Forgery Verification Data<br>Rule: hex(sha256(tid + ediDate + SecretKey)) |
| `returnCharSet`  | String    | No          | 10    | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |
| `taxFreeAmt`     | Int       | No          | 12    | Tax-free part of the cancellation amount, a whole number with no decimal point<br>For a partial cancellation, the value must not exceed the tax-free amount not yet cancelled, or the request fails with [`U319`](../code/nicepay-code.md#api-response-code) |
| `refundAccount`  | String    | Conditional | 16    | Refund account number<br>Required when you cancel a virtual account payment after the customer has deposited the money (a refund) |
| `refundBankCode` | String    | Conditional | 3     | Bank code of the refund account, see [Bank code](../code/nicepay-code.md#bank-code)<br>Required when you cancel a virtual account payment after the customer has deposited the money (a refund) |
| `refundHolder`   | String    | Conditional | 10    | Holder name of the refund account<br>Required when you cancel a virtual account payment after the customer has deposited the money (a refund) |

<br><br>


### Response parameter (for tid or sessionId cancel)

```bash
POST
Content-type: application/json
```

Dates and times that NicePay returns are in Korea Standard Time (KST, UTC+9), for example `2023-03-24T14:04:16.982+0900`. See [Dates in responses](../info/nicepay-info-general.md#dates-in-responses) for how to parse them.

Required: Yes = has a non-empty value in every response whose `resultCode` is `0000`; No = can be `null`, empty, or left out. For a field of an object or array, Yes applies whenever that object or array element is present.

| Parameter | Type | Required | Bytes | Description |
|:----------|:----:|:--------:|:------:|:-----------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `tid` | String | Yes | 30 | NICEPAY transaction ID |
| `cancelledTid` | String | No | 30 | Cancellation transaction ID<br>- Responded only with cancellation requests<br>- Use when finding canceled transaction information in the cancels object. |
| `orderId` | String | Yes | 64 | Unique order number |
| `sessionId` | String | No | 256 | Checkout session ID of the payment<br>Always returned when you cancel with `sessionId`. When you cancel with `tid`, returned only when the payment was made through Checkout; otherwise the key is left out |
| `ediDate` | String | Yes | - | Response message creation date and time (ISO 8601 format) |
| `signature` | String | Yes | 256 | Forgery verification data<br>Rule: hex(sha256(tid + amount + ediDate + SecretKey)), see [Verifying the payment result](./nicepay-api-payment-window-url.md#verifying-the-payment-result)<br>Covers only `tid`, `amount` and `ediDate`. Check `resultCode` and `status` separately |
| `status` | String | Yes | 20 | Payment processing status<br>paid: payment completed<br>ready: virtual account issued, not paid yet<br>failed: payment failed<br>cancelled: cancelled<br>partialCancelled: partially cancelled<br>['paid', 'ready', 'failed', 'cancelled', 'partialCancelled'] |
| `paidAt` | String | Yes | - | Time of payment completed ISO 8601 format<br>If payment is not completed, return 0<br>For a virtual account that is not paid yet, the time the account number was requested |
| `failedAt` | String | Yes | - | Time of payment failure ISO 8601 format<br>If not payment is not failed, return 0 |
| `cancelledAt` | String | Yes | - | Payment cancellation time ISO 8601 format<br>If it is not cancellation request, return 0<br>In case of partial cancellation, the last cancellation time will be return |
| `payMethod` | String | Yes | 10 | Payment method<br><br>card: credit card, <br>vbank: virtual account, <br>bank: account transfer, <br>cellphone: mobile phone, <br>naverpay=Naver Pay, <br>kakaopay=Kakao Pay, <br>samsungpay=Samsung Pay, <br>payco=Payco, <br>ssgpay=SSG Pay, <br>tosspay=Toss Pay |
| `amount` | Int | Yes | 12 | payment amount |
| `balanceAmt` | Int | Yes | 12 | Remained balance for cancellation |
| `goodsName` | String | Yes | 40 | Product name |
| `mallReserved` | String | No | 500 | Spare field for store information delivery<br>It is recommended to use JSON string format.<br>However, double quotation marks (") cannot be used |
| `useEscrow` | Boolean | Yes | - | Escrow transaction status<br> true: Escrow transaction |
| `currency` | String | Yes | 3 | Approved currency<br>KRW: Korean Won, USD: USD, CNY: Yuan |
| `channel` | String | No | 10 | pc:PC payment, mobile:mobile payment<br>['pc', 'mobile', 'null'] |
| `approveNo` | String | No | 30 | Authorization Number<br>Credit Card, Bank Transfer, Mobile Phone<br>Only populated when cancelling with `tid`; always `null` when cancelling with `sessionId` |
| `buyerName` | String | No | 30 | Buyer name |
| `buyerTel` | String | No | 40 | Buyer phone number |
| `buyerEmail` | String | No | 60 | Buyer Email |
| `issuedCashReceipt` | Boolean | Yes | - | Issuance status of cash receipts<br>true: issued / false: not issued |
| `receiptUrl` | String | No | 200 | URL for receipt|
| `mallUserId` | String | No | 20 | User ID managed by the store |
| `cellphone` | Object | No | - | Always `null`, also for mobile phone payments (`payMethod` `cellphone`). Mobile phone payment details are in the top-level fields |
| `messageSource` | String | Yes |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|

<br>

#### Coupon information <img alt="Object type" src="https://img.shields.io/badge/-Object-F7DF1E"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-555555">

| Parameter | Field     |   Type   |  Required   |  Bytes  |    Description     |
|:----------|:----------|:--------:|:-----:|:-------:|:--------------|
| `coupon`    |           | Object   | No    | - | Information for instant discount promotion |
|           | `couponAmt` | Int      | Yes   | 12 | Amount of instant discount applied |

<br>

#### Card information <img alt="Object type" src="https://img.shields.io/badge/-Object-F7DF1E"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-555555">

Same fields and rules as [Card information](./nicepay-api-retrieve.md#card-information--) in Transaction Status Inquiry.

| Parameter | Field          |   Type   |  Required   |  Bytes  | Description   |
|:----------|:---------------|:--------:|:-----:|:-------:|:------------------|
| `card` | | Object | No | | Credit Card Object<br>`null` for virtual account, bank transfer and mobile phone payments, and for a failed payment |
| | `cardCode` | String | Yes | 3 | Card company code |
| | `cardName` | String | Yes | 20 | Card issuer name <br> ex) BC |
| | `cardNum` | String | No | 20 | Card number, masked: for a card number of 13 digits or more, the first 6 and the last 4 digits are shown and the digits between them are replaced with `*`<br>Ex) `123412******1234`<br>- Kakao Money/Naver Point/Payco Point used for payment 'null' will be return. |
| | `cardQuota` | Int | Yes | 3 | Installment Month<br>0: lump sum, 2:2 months, 3:3 months … |
| | `isInterestFree` | Boolean | No | - | The store pays the customer's installment interest<br>true: yes, false: no, null: not reported for this payment |
| | `cardType` | String | No | 6 | Card type<br>credit:credit card, check:debit |
| | `canPartCancel` | Boolean | No | - | Whether partial cancellation is possible<br>true: Possible, false: Impossible, null: not reported by the acquirer for this card |
| | `acquCardCode` | String | Yes | 3 | Acquirer code |
| | `acquCardName` | String | Yes | 100 | Acquirer Name |

<br>

#### Cash receipts information <img alt="Array type" src="https://img.shields.io/badge/-Array-blueviolet"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-555555">

| Parameter     | Field        |   Type   |   Required   |  Bytes  | Description |
|:--------------|:-------------|:--------:|:------:|:------:|:-------------------|
| `cashReceipts` | | Array | No | | Cash Receipt Issuance Information<br>-When the customer used Naverpay Points and Virtual Account, this value will be return.<br>-In case of partial cancellation, array will be more than 2 |
| | `receiptTid` | String | Yes | 30 | Cash Receipt TID |
| | `orgTid` | String | Yes | 30 | Related original approval/cancel transaction ID.<br>In case of partial cancellation, it is mapped with the original TID. |
| | `status` | String | Yes | 20 | issueRequested : Issuance Requested <br>issueReqCancelled : Issuance Request cancelled<br>issued: Issuance completed by the National Tax Service <br>issueFailed: Issuance failed<br>cancelRequested: Cancellation requested <br>cancelReqCancelled: issuance Cancelled by the National Tax Service<br>cancelled: Cancellation completed <br>cancelFailed: Cancellation failed |
| | `amount` | Int | Yes | 12 | Total amount of cash receipt issued |
| | `taxFreeAmt` | Int | Yes | 12 | Tax-free amount of the cash receipt |
| | `receiptType` | String | Yes | 20 | Cash Receipt Type<br>individual: For personal income deduction<br>company: For proof of business expenses |
| | `issueNo` | String | Yes | 30 | Issued number by the National Tax Service |
| | `receiptUrl` | String | Yes | 200 | Cash Receipt URL for user |

<br>

#### Bank transfer information <img alt="Object type" src="https://img.shields.io/badge/-Object-F7DF1E"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-555555">

| Parameter  | Field     |   Type   |  Required   |  Bytes  | Description  |
|:-----------|:----------|:--------:|:-----:|:------:|:------------------|
| `bank`     |            |  Object  | No    |        | bank object    |
|            | `bankCode`  |  String  |  Yes  |   3    | bank code  |
|            | `bankName`  |  String  |  Yes  |   20   | Bank name |

<br>

#### Virtual Account information <img alt="Object type" src="https://img.shields.io/badge/-Object-F7DF1E"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-555555">

| Parameter | Field        |  Type   |  Required   |  Bytes  | Description  |
|:----------|:-------------|:-------:|:-----:|:------:|:-------------------------|
| `vbank` | | Object | No | | Virtual account object |
| | `vbankCode` | String | Yes | 3 | Virtual account bank code |
| | `vbankName` | String | Yes | 20 | Virtual account bank name |
| | `vbankNumber` | String | Yes | 20 | Virtual account number |
| | `vbankExpDate` | String | No | - | Expiration Date<br>ISO 8601 format |
| | `vbankHolder` | String | Yes | 40 | Virtual account holder name |

<br>

#### Cancel information <img alt="Array type" src="https://img.shields.io/badge/-Array-blueviolet"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-555555">

| Parameter | Field       |  Type   |  Required   |  Bytes  | Description  |
|:----------|:------------|:-------:|:-----:|:-------:|:-----------------------|
| `cancels` | | Array | No | | Cancellation history |
| | `tid` | String | Yes | 30 | Cancel Transaction ID |
| | `amount` | Int | Yes | 12 | Cancellation Amount |
| | `cancelledAt` | String | Yes | - | Canceled Time<br>ISO 8601 format |
| | `reason` | String | Yes | 100 | Cancellation reason |
| | `receiptUrl` | String | Yes | 200 | <br>Receipt URL for user n |
| | `couponAmt` | Int | No | 12 | Cancellation amount of coupon <br> *Optional|

<br>

<br><br>

## Net cancel

### Net cancel Over-view

Use a net cancel when your Merchant Server cannot confirm the result of a payment, for example after a read timeout or a network error while it waits for the approval response or the `returnUrl` callback. NicePay finds the payment by the `orderId` that you sent with it and cancels the full amount, so you do not need the `tid`. For the steps after a timeout, see [Timeout Information](../info/nicepay-info-firewall-timeout.md#timeout-information).

> **⚠️ Important:** A net cancel works only within 1 hour after the payment. A later one fails with [`2020`](../code/nicepay-code.md#api-response-code): cancel that payment with [Cancel](#cancel-request-parameter-with-tid) instead.  
> To cancel a payment that you know succeeded, use [Cancel](#cancel-request-parameter-with-tid), not a net cancel.  
> Net cancel is available in Sandbox and Live.  

<br>

### Net cancel example

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/payments/netcancel' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
--data '{
    "orderId" : "merchant-order-id"
}'
```

<br>

### Net cancel Request parameter

```bash
POST /v1/payments/netcancel  
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter     | Type      | Required | Bytes | Description |
|:--------------|:---------:|:--------:|:------:|:-----------|
| `orderId`        | String    | Yes | 64    | The `orderId` of the payment to cancel |
| `mallReserved`   | String    | No          | 500   | Spare field for store information delivery |
| `ediDate`        | String    | Conditional | -     | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `signData`       | String    | No          | 256   | Forgery Verification Data<br>Rule: hex(sha256(orderId + ediDate + SecretKey)) |
| `returnCharSet`  | String    | No          | 10    | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |

<br>

### Net cancel Response parameter

The response has the same fields as the Cancel response, see [Response parameter (for tid or sessionId cancel)](#response-parameter-for-tid-or-sessionid-cancel). `resultCode` is `0000` when NicePay cancelled the payment.

Related error codes: `U100`, `U105`, `U106`, `U107`, `U312`, `2020`, see [API Response code](../code/nicepay-code.md#api-response-code). An `orderId` that matches no payment of your merchant account fails with `U107`.

<br>
