## Recurring Payment

This page covers four APIs:

- [Create Token](#recurring-payment---create-token) (`POST /v1/subscribe/regist`): register a card once and receive a token (`bid`).
- [Authorization](#recurring-payment---authorization) (`POST /v1/subscribe/{bid}/payments`): charge the token each time a payment is due.
- [Delete Token](#delete-token) (`POST /v1/subscribe/{bid}/expire`): delete the token when the subscription ends.
- [Bid Status Inquiry](#bid-status-inquiry) (`POST /v1/subscribe/{bid}/status`): check whether a Toss Pay token is still active.

Create Token sends raw card details from your server. See [PCI-DSS Overview](../info/nicepay-info-pci-dss.md) for what that generally implies, and confirm the specific requirements for your account with NicePay. A token registered through Checkout with a billing `method` such as `cardBill` keeps card details off your server, see [Which Integration Should I Use?](../INTEGRATION-PATHS.md).

<br>

## Recurring Payment - Create Token

You can implement subscribtion payments through the Recurring Payment API.  
If you pass the card information through the `/v1/subscribe/regist` API, you can receive an encrypted Token(bid) in response.  
After that, if you pass the encrypted Token(bid) through the `/v1/subscribe/{bid}/payments` API with payment amount, Authorization will be occurred with the registered card.  

> **⚠️ Important:** Multiple Token can be generated with one card and All issued bid can be used until deleted.  
> This describes NicePay's own token limit only, see the `F201` note under [Create Token(bid) Response Parameter](#create-tokenbid-response-parameter) for a case where a `regist` call can still fail on an already-registered card.  

<br>

**Before you start**, you'll need:
- A [Client and Secret key](../info/nicepay-info-key.md) issued from the NicePay admin console
- An `Authorization` header built from those keys, see [Basic and Bearer authentication](../info/nicepay-info-basic-token.md)
- We recommend testing against the Sandbox first, then switching to Live once verified. See [Recurring Payment in Sandbox](../info/nicepay-info-sandbox.md#recurring-payment-in-sandbox) for what Sandbox checks and returns
- A merchant ID for card payments without customer authentication on your client key, the same one that [Key-in Payment](./nicepay-api-keyin.md) uses. NicePay sets it up for your account. Without it, Create Token fails with [`U107`](../code/nicepay-code.md#api-response-code), although that code otherwise means that a transaction was not found, and Delete Token and Bid Status Inquiry fail with [`U313`](../code/nicepay-code.md#api-response-code)

<br>

### Create Token Over-view
<a href="../image/payment-subscribe.svg"><img alt="Sequence diagram of the whole recurring payment lifecycle (create token, authorization, optional token deletion), emphasizing this section's Create Token steps" src="../image/payment-subscribe.svg" width="800px"></a>

### Example code

**Default (AES-128, no `encMode`)**

```bash
curl -X POST 'https://sandbox-api.nicepay.co.kr/v1/subscribe/regist' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' 
--data '{
    "encData": "2127975b6d82c36136ba8197a997a994f6c086ff75a6d35e514c54a1e686545e60b76f11bec706de1082e43dd74ae5c5f0709dc1eca6c3cd20e1c0e9e9b7a85c6505461c91c865d82072e41ba5284bd7",
    "orderId": "merchant-order-id"
}'
```

> These examples call Sandbox with the public Sandbox key from [Test key information](../info/nicepay-info-sandbox.md#test-key-information). The `encData` values are placeholders that NicePay cannot decrypt (`F101`): encrypt your own card fields with the Sandbox Secret key, as in the encryption examples below.  
> `encData` here is encrypted with AES-128 (see [encData Field Encryption Example (AES-128)](#encdata-field-encryption-example-aes-128) below). No `encMode` field is sent, so the default algorithm applies.

**AES-256 (`encMode=A2`)**

```bash
curl -X POST 'https://sandbox-api.nicepay.co.kr/v1/subscribe/regist' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' 
--data '{
    "encData": "C41346B71984...",
    "orderId": "merchant-order-id",
    "encMode" : "A2"
}'
```

<br>

### Create Token(bid) Request Parameter

```bash
POST /v1/subscribe/regist  
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

Required: Yes = always send; No = optional; Conditional = send in the case stated in Description. Bytes = maximum length in bytes.

| Parameter     | Type      | Required | Bytes | Description |
|:--------------|:--------:|:-----:|:------:|:---------------|
| `encData`       |  String  |   Yes   |  512   | Payment Information Encryption Data<br>- Encryption Algorithm: AES128<br>- Encryption Details: AES/CBC/PKCS5padding<br>- Encoding Encryption Result: Hex Encoding<br>- Encryption KEY: 16 digits before SecretKey<br>- IV : 16 digits before SecretKey<br><br> Hex(AES(cardNo=value&expYear=YY&expMonth=MM&idNo=value&cardPw=value)) |
| `orderId`       |  String  |   Yes   |   64   | Unique order number or payment number managed by the merchant |
| `buyerName`     |  String  |   No   |   30   | Buyer name  |
| `buyerEmail`    |  String  |   No   |   60   | Buyer Email |
| `buyerTel`      |  String  |   No   |   20   | Buyer phone number<br> *Number only|
| `encMode`       |  String  |   No   |   10   | Encryption Mode<br>`encData` Field Encryption Algorithm Definition<br><br> A2 : AES256<br>Encryption Algorithm : AES256<br> Encryption Detail : AES/CBC/PKCS5padding <br> Encryption Result Encoding : Hex Encoding <br> *Encryption KEY: SecretKey (32byte)<br>•IV: 16 digits before the SecretKey |
| `ediDate`       |  String  |   Conditional   |   -    | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `signData`      |   String    |   No   |  256   | Forgery Verification Data<br> Rule : hex(sha256(orderId + ediDate +   SecretKey)) |
| `returnCharSet` | String    |   No    | 10        | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |

<br>

### encData Field Details

NicePay checks `encData` against the encryption and authentication level of your merchant account with the same rules as Key-in, see the level table and the note in [Key-in encData Field Details](./nicepay-api-keyin.md#encdata-field-details). On this API, a failed check returns [`U317`](../code/nicepay-code.md#api-response-code) instead of `U341`, in Sandbox and in Live, and `encData` that NicePay cannot decrypt returns [`F101`](../code/nicepay-code.md#api-response-code). The encryption examples below contain both `idNo` and `cardPw`, as for level 11: leave out the fields that your level does not list. For overseas-issued cards, see [Accepting overseas customers](../info/nicepay-info-general.md#accepting-overseas-customers).

| Parameter     | Type      | Required | Bytes | Description |
|:--------------|:--------:|:-----:|:------:|:---------------|
| `cardNo`     |  String  |     Yes      |   16   | Card Number<br>Numbers only     |
| `expYear`    |  String  |     Yes      |   2    | expiration year<br>format : YY  |
| `expMonth`   |  String  |     Yes      |   2    | expiration month<br>format : MM  |
| `idNo`       |  String  |  Conditional  |   13   | Card holder's date of birth, 6 digits: YYMMDD (for example `800101` for 1 January 1980)<br>Corporate card: Korean business registration number, 10 digits<br>Required when your merchant's level is 10 or 11 |
| `cardPw`     |  String  |  Conditional  |   2    | First 2 digits of the 4-digit card password of a Korean card<br>Required when your merchant's level is 03 or 11 |

<br>

### encData Field Encryption Example (AES-128)

```bash
- Plain-text  : cardNo=1234567890123456&expYear=25&expMonth=12&idNo=800101&cardPw=12
- Encryption-key : 2dcc2a0d63bf4694 (16 digits before SecretKey)
- IV : 2dcc2a0d63bf4694 (16 digits before SecretKey)

- Encrypted-text : `2127975b6d82c36136ba8197a997a994f6c086ff75a6d35e514c54a1e686545e60b76f11bec706de1082e43dd74ae5c5f0709dc1eca6c3cd20e1c0e9e9b7a85c6505461c91c865d82072e41ba5284bd7`
```

<br>

### encData Encryption Example (AES-256)
```bash
- Plain-text : cardNo=1234567890123456&expYear=25&expMonth=12&idNo=800101&cardPw=12
- Encryption-key : 2dcc2a0d63bf469490bb19a201be3735
- IV  : 2dcc2a0d63bf4694 (16 digits before SecretKey)

- Encrypted-text : `6ecfe97e521bc67c3053d74a9dbdba53033d343fc9e8e38e730964b22ef2e4a59607171b00a9da977141b3f79fffa1e80a16c08bc58666b479f554a966a363414347e62f2621f8df220c7a4a545592d0`
```

<br>

### Create Token(bid) Response Parameter

```bash
POST
Content-type: application/json
```

Dates and times that NicePay returns are in Korea Standard Time (KST, UTC+9), for example `2023-03-24T14:04:16.982+0900`. See [Dates in responses](../info/nicepay-info-general.md#dates-in-responses) for how to parse them.

Required: Yes = has a non-empty value in every response whose `resultCode` is `0000`; No = can be `null`, empty, or left out. For a field of an object or array, Yes applies whenever that object or array element is present.

| Parameter     | Type      | Required | Bytes | Description |
|:--------------|:---------:|:--------:|:-----:|:------------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg`  | String | Yes | 100 | Result message |
| `tid`        | String | Yes |  30   | Transaction ID<br>Ex) nictest00m01011104191651325596  |
| `orderId`    | String | Yes | 64        | Your Unique order ID *Not reusable |
| `bid`        | String | No |  30   | Token<br>- Key value linked to card information, delivered when calling Token Authorization API<br>Ex) BIKYnictest00m1104191651325596  |
| `authDate`   | String | No |   -   | Date created<br>ISO 8601 format   |
| `cardCode`   | String | No |   3   | Card company code |
| `cardName`   | String | No |  20   | Card issuer name <br> ex) BC   |
| `messageSource` | String | Yes |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|
| `status`     | String | Yes |   6   | issued: bid was created successfully<br>failed: `regist` call failed, see `resultCode` |

> **⚠️ Important:** Even though NicePay allows multiple tokens per card, a `regist` call can still fail with [`F201`](../code/nicepay-code.md#api-response-code) ("card already registered", bill key issuance failed), returned as-is in `resultCode` with `status: failed`, no `bid`, and `messageSource: external`. That check happens on the card issuer/payment network side, not NicePay's, so the exact conditions that trigger it are not documented here; [open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) if you hit it unexpectedly.  
> `F201` is specific to card-based bill-key issuance (this API, and Checkout's `cardBill` method, see [Hosted Payment Page Request Parameter](./nicepay-api-payment-window-url.md#hosted-payment-page-request-parameter)); Recurring Payment enrolled through Naver Pay/Kakao Pay/Toss Pay checkout (`naverCardBill`/`naverPointBill`/`kakaoBill`/`tosspayBill`) goes through a separate corePG code family and is not affected by it.  

Related error codes: `A253`, `F101`, `F110`, `F115`, `F116`, `U107`, `U317`, see [API Response code](../code/nicepay-code.md#api-response-code).

<br>


## Recurring Payment - Authorization
- Token(bid) authorization means payment (approval) processing through the issued Token(bid).
- If you call the `/v1/subscribe/{bid}/payments` API through the registered Token(bid), payment (approval) will be processed.
- For Token(bid) approval, the `Create Token`process is required.
- Available in Sandbox, see [Recurring Payment in Sandbox](../info/nicepay-info-sandbox.md#recurring-payment-in-sandbox).

<br>

### Authorization Over-view
<a href="../image/payment-subscribe-authorization.svg"><img alt="Sequence diagram of the whole recurring payment lifecycle (create token, authorization, optional token deletion), emphasizing this section's Authorization steps" src="../image/payment-subscribe-authorization.svg" width="800px"></a>

### Token authorization example
```bash
curl -X POST 'https://sandbox-api.nicepay.co.kr/v1/subscribe/BIKYnicuntct2m2107272028532670/payments' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' 
--data '{
    "orderId": "merchant-order-id",
    "amount": 1004,
    "goodsName": "your-goods-name",
    "cardQuota": 0,
    "useShopInterest": false
}'
```

<br>

### Recurring Payment - Authorization Request Parameter

```bash
POST /v1/subscribe/{bid}/payments  
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter     | Type      | Required | Bytes | Description |
|:--------------|:---------:|:--------:|:------:|:-----------|
| `orderId`         |  String  |   Yes   |   64   | Your unique order ID. It must differ from every `orderId` that your merchant account has used, including Key-in payments and partial cancellations.<br>After a declined charge (`status` `failed`), NicePay releases the `orderId`, so you can use it again |
| `amount`          |   Int    |   Yes   |   12   | Payment amount in Korean won<br>Whole number with no decimal point. Recurring Payment accepts KRW only. See [Amounts and currencies](../info/nicepay-info-general.md#amounts-and-currencies) |
| `goodsName`       |  String  |   Yes   |   40   | Product name  |
| `method`          |  String  |   No   |   20   | Payment method the token was issued under<br>Leave empty for a card-issued token (default)<br>`naverCardBill` / `naverPointBill` / `kakaoBill` / `tosspayBill` for a token issued through the corresponding Easy Pay checkout |
| `cardQuota`       |   Int    |   Conditional  |   2    | Installment Month<br>0: Pay in full amount, 2:2 months, 3:3 months …<br>Required when `method` is not `tosspayBill`; must be omitted when `method` is `tosspayBill` (rejected with `U143` otherwise) |
| `useShopInterest` | Boolean  |   Conditional  |   -    | The store pays the installment interest of the customer<br>(currently, only false is available)<br>Required when `method` is not `tosspayBill`; must be omitted when `method` is `tosspayBill` (rejected with `U143` otherwise) |
| `useCardPoint`    | Boolean  |   No   |   -    | Whether the card company's points may be used for this charge<br>`false`: not used (default) / `true`: used |
| `buyerName`       |  String  |   No   |   30   | Buyer name |
| `buyerTel`        |  String  |   No   |   20   | Buyer phone number<br>*Number only   |
| `buyerEmail`      |  String  |   No   |   60   | Buyer Email |
| `taxFreeAmt`      |   Int    |   No   |   12   | Tax-free part of `amount`<br>Whole number with no decimal point, not greater than `amount`: a larger value fails with [`U321`](../code/nicepay-code.md#api-response-code). See [Tax breakdown](../info/nicepay-info-general.md#tax-breakdown) |
| `supplyAmt`       |   Int    |   No   |   12   | Supply amount, the pre-VAT portion of `amount`<br>Ignored on this API: NicePay computes it from `amount` and `taxFreeAmt`, see the note below |
| `goodsVat`        |   Int    |   No   |   12   | VAT portion of `amount`<br>Ignored on this API, see the note below |
| `serviceAmt`      |   Int    |   No   |   12   | Service charge portion of `amount`<br>Ignored on this API, see the note below |
| `mallReserved`    |  String  |   No   |  500   | Spare field for store information delivery<br>It is recommended to use JSON string format.<br>However, double quotation marks (") cannot be used  |
| `ediDate`         |  String  |   Conditional   |   -    | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `signData`        |  String  |   No   |  256   | Forgery Verification Data<br> Rule : hex(sha256(orderId + bid + ediDate + SecretKey))      |
| `returnCharSet` | String    |   No    | 10        | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |

> **⚠️ Important:** On this API, NicePay ignores the `supplyAmt`, `goodsVat` and `serviceAmt` that you send. NicePay computes them from `amount` and `taxFreeAmt` (0 when you leave it out), so the four values always add up to `amount`. To change the tax split, send `taxFreeAmt`. See [Tax breakdown](../info/nicepay-info-general.md#tax-breakdown) for the formula.  

<br>

### Recurring Payment - Authorization Response Parameter

```bash
POST
Content-type: application/json
```

| Parameter | Type | Required | Bytes | Description |
|:----------|:----:|:--------:|:------:|:-----------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `tid` | String | Yes | 30 | NICEPAY transaction ID |
| `cancelledTid` | String | No | 30 | Cancellation transaction ID<br>Always `null` in this response |
| `orderId` | String | Yes | 64 | Unique order number |
| `ediDate` | String | Yes | - | Response message creation date and time (ISO 8601 format) |
| `signature` | String | Yes | 256 | Forgery verification data<br>Rule: hex(sha256(tid + amount + ediDate + SecretKey)), see [Verifying the payment result](./nicepay-api-payment-window-url.md#verifying-the-payment-result)<br>Covers only `tid`, `amount` and `ediDate`. Check `resultCode` and `status` separately |
| `status` | String | Yes | 20 | Payment processing status<br>paid: payment completed<br>failed: payment failed<br>['paid', 'failed'] |
| `paidAt` | String | Yes | - | Time of payment completed ISO 8601 format<br>If payment is not completed, return 0 |
| `failedAt` | String | Yes | - | Time of payment failure ISO 8601 format<br>If not payment is not failed, return 0 |
| `cancelledAt` | String | Yes | - | Always 0 in this response<br>To see later cancellations of this charge, use [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id) |
| `payMethod` | String | Yes | 10 | Payment method<br><br>card: credit card, <br>naverpay=Naver Pay, <br>kakaopay=Kakao Pay, <br>samsungpay=Samsung Pay, <br>tosspay=Toss Pay |
| `amount` | Int | Yes | 12 | payment amount |
| `balanceAmt` | Int | Yes | 12 | Remained balance for cancellation |
| `goodsName` | String | Yes | 40 | Product name |
| `mallReserved` | String | No | 500 | Spare field for store information delivery<br>It is recommended to use JSON string format.<br>However, double quotation marks (") cannot be used |
| `useEscrow` | Boolean | Yes | - | Escrow transaction status<br> true: Escrow transaction |
| `currency` | String | Yes | 3 | Approved currency<br>Always `KRW` (Korean Won) for Recurring Payment |
| `channel` | String | No | 10 | pc:PC payment, mobile:mobile payment<br>['pc', 'mobile', 'null'] |
| `approveNo` | String | No | 30 | Authorization Number<br>Credit Card, Bank Transfer, Mobile Phone |
| `buyerName` | String | No | 30 | Buyer name |
| `buyerTel` | String | No | 40 | Buyer phone number |
| `buyerEmail` | String | No | 60 | Buyer Email |
| `issuedCashReceipt` | Boolean | Yes | - | Issuance status of cash receipts<br>true: issued / false: not issued |
| `receiptUrl` | String | No | 200 | URL for receipt|
| `mallUserId` | String | No | 20 | User ID managed by the store |
| `messageSource` | String | Yes |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|


<br>

#### Coupon information <img alt="Object type" src="https://img.shields.io/badge/-Object-F7DF1E"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-555555">

| Parameter | Field     |   Type   |  Required   |  Bytes  |    Description     |
|:----------|:----------|:--------:|:-----:|:-------:|:--------------|
| `coupon`    |           | Object   |  No   | - | Information for instant discount promotion |
|           | `couponAmt` | Int      |  Yes  | 12 | Amount of instant discount applied |

<br>

#### Card information <img alt="Object type" src="https://img.shields.io/badge/-Object-F7DF1E">

Same fields and rules as [Card information](./nicepay-api-retrieve.md#card-information--) in Transaction Status Inquiry.

| Parameter | Field          |   Type   |  Required   |  Bytes  | Description   |
|:----------|:---------------|:--------:|:-----:|:-------:|:------------------|
| `card` | | Object | Yes | | Credit Card Object |
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

### After a Recurring Payment charge

- A charge made with `/v1/subscribe/{bid}/payments` does not have its own cancel API. Cancel or refund it with the standard [Cancel request with tid](./nicepay-api-cancel.md#cancel-request-parameter-with-tid), using the `tid` from the Authorization Response above.
- You can look up a charge anytime via [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id) with the `tid`. A declined charge can be looked up by `tid` only: a lookup by `orderId` returns [`U107`](../code/nicepay-code.md#api-response-code).
- If the charge call times out, look the charge up by `orderId` as described in [Timeout Information](../info/nicepay-info-firewall-timeout.md#timeout-information).
- A declined charge (`status` `failed`, `resultCode` other than `0000`) has a `tid`. NicePay releases its `orderId`, so you can send the charge again with the same `orderId`. NicePay sends no webhook for a declined charge.
- A token (`bid`) is not deleted automatically and stays usable until you remove it. See [Delete Token](#delete-token) below.

The table below lists the error codes of `/v1/subscribe/{bid}/payments`. See [API Response code](../code/nicepay-code.md#api-response-code) for the messages.

| Code | Meaning | What to do |
|:---|:---|:---|
| `U100` | A required field is missing: `orderId`, `amount`, `goodsName`, or `cardQuota` or `useShopInterest` when `method` is not `tosspayBill` | Fix the request and send it again. NicePay has not used the `orderId` |
| `U105` | `orderId` or `mallReserved` is too long | Same as `U100` |
| `U127` | `amount` has 13 digits or more | Same as `U100` |
| `U143` | `cardQuota` or `useShopInterest` was sent with `method` `tosspayBill` | Same as `U100` |
| `U312` | `signData` does not match | Same as `U100` |
| `U321` | `taxFreeAmt` is greater than `amount` | Same as `U100` |
| `U345` | `bid` is empty or is not 30 bytes long | Same as `U100` |
| `U309` | No token with this `bid` was issued for your `clientId` | Send the charge with the `clientId` that registered the token, or register the card again with [Create Token](#recurring-payment---create-token). NicePay has not used the `orderId` |
| `U112` | The `orderId` is in use or was used | Do not charge again with a new `orderId`. Look the charge up by `orderId` first, see [Timeout Information](../info/nicepay-info-firewall-timeout.md#timeout-information) |
| `U503` | NicePay's processing failed, and NicePay cancelled the charge (net cancel). The response has no `tid` | Look the charge up by `orderId` and follow the Recurring row of [Timeout Information](../info/nicepay-info-firewall-timeout.md#timeout-information) |
| `U506` | NicePay could not record the `orderId` | Same as `U503` |
| Other codes, with `status` `failed` | The payment network declined the charge | The response has a `tid`. You can send the charge again with the same `orderId` |

<br>


## Delete Token

`Delete Token` refers to the process of deleting the issued Token(bid).  
If you pass the registered billkey to the `/v1/subscribe/{bid}/expire` API, the Token(bid) will be deleted.  
Deleted Token(bid) cannot be restored or approved. 
Available in Sandbox, see [Recurring Payment in Sandbox](../info/nicepay-info-sandbox.md#recurring-payment-in-sandbox).

<br>

### Delete Token Over-view
<a href="../image/payment-subscribe-delete.svg"><img alt="Sequence diagram of the whole recurring payment lifecycle (create token, authorization, optional token deletion), emphasizing this section's Delete Token steps" src="../image/payment-subscribe-delete.svg" width="800px"></a>

> **⚠️ Important:** This request is fully synchronous end-to-end: the client should keep waiting (e.g. a loading indicator) from the moment cancellation is requested until the `/v1/subscribe/{bid}/expire` response comes back through the merchant server. There is no webhook or async callback for this flow, see [Timeout Information](../info/nicepay-info-firewall-timeout.md#timeout-information) for the underlying request timeout values.  

### Delete Token(bid) Example code

``` bash
curl -X POST 'https://sandbox-api.nicepay.co.kr/v1/subscribe/BIKYnicuntct2m2107272028532670/expire' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' 
--data '{
    "orderId": "your-order-id"
}'
```

<br>

### Delete Token(bid) Request Parameter

```bash
POST /v1/subscribe/{bid}/expire   
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter     | Type   | Required | Bytes | Description |
|:--------------|:------:|:--------:|:------:|:-----------|
| `orderId`       | String |  Yes       |  64   | Your unique order ID *Not reusable |
| `method` | String | Conditional | 20 | Payment method the token was issued under. See the table below.<br>Required when the token was issued under `naverCardBill`, `naverPointBill`, `kakaoBill` or `tosspayBill` |
| `reason` | String | Conditional | 100 | Reason for deletion. See the table below.<br>If `method` is `naverCardBill`, `naverPointBill` or `tosspayBill` and `reason` is missing or blank, the request fails with [`U100`](../code/nicepay-code.md#api-response-code).<br>Required when `method` is `naverCardBill`, `naverPointBill` or `tosspayBill` |
| `ediDate`       | String |     Conditional     |   -   | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `signData`      | String |     No     |  256  | Forgery Verification Data<br>Rule : hex(sha256(orderId + bid +   ediDate + SecretKey))|
| `returnCharSet` | String |     No     |  10   | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |	

Which `method` and `reason` to send depends on how the token was issued:

| Token issued through | `method` to send | `reason` |
|:---|:---|:---|
| `/v1/subscribe/regist` | Leave it out, or send `cardBill` | Not needed. NicePay does not forward it. |
| Checkout with `method: cardBill` | Leave it out, or send `cardBill` | Not needed. NicePay does not forward it. |
| Checkout with `method: naverCardBill` or `naverPointBill` | The same value | Required |
| Checkout with `method: kakaoBill` | `kakaoBill` | Not needed. NicePay does not forward it. |
| Checkout with `method: tosspayBill` | `tosspayBill` | Required. NicePay forwards the first 100 bytes to Toss Pay. |

<br>

### Delete Token(bid) Response Parameter

```bash
POST
Content-type: application/json
```

| Parameter | Type | Required | Bytes | Description |
|:----------|:----:|:--------:|:------:|:-----------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `tid` | String | Yes | 30 | NICEPAY transaction ID |
| `orderId` | String | Yes | 64 | Your Unique order ID |
| `bid`        | String | Yes  | 30   | Token |
| `authDate`   | String | Yes | -    | ISO 8601 format<br>*Not returned only when the request fails local validation (e.g. missing `orderId`) before reaching NicePay; present regardless of whether the deletion itself succeeded or failed |

> **⚠️ Important:** Deleting a Token(bid) that is already deleted or does not exist returns [`U115`](../code/nicepay-code.md#api-response-code) ("Deleted BID"), not `0000`. NicePay normalizes the payment-network code behind it, so you get `U115` whether the token was a card billkey or an easy-pay (NaverPay/KakaoPay/TossPay) one. Treat `U115` as "already gone" rather than as a retryable failure.  
> A successful card-billkey deletion always returns `0000`. You will not see [`F101`](../code/nicepay-code.md#api-response-code) here even though the payment network uses it for this case internally; on this API `F101` only ever means a signature/encryption verification failure.  

Related error codes: `U115`, `U313`, `A255`, see [API Response code](../code/nicepay-code.md#api-response-code).

<br>

## Bid Status Inquiry

`Bid Status Inquiry` looks up whether an issued Token(bid) is currently active or has been suspended on the payment network side.  
If you pass the registered billkey to the `/v1/subscribe/{bid}/status` API, NicePay returns its current status.

> **⚠️ Important:** This API currently supports Toss Pay-issued tokens only (`method: tosspayBill`); any other `method` value is rejected with `U119`.  
> This API does not work in [Sandbox](../info/nicepay-info-sandbox.md#sandbox-limitations), because Sandbox cannot issue Toss Pay tokens.  

<br>

### Bid Status Inquiry Example code

```bash
curl -X POST 'https://api.nicepay.co.kr/v1/subscribe/BIKYnicuntct2m2107272028532670/status' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic <credentials>' 
--data '{
    "orderId": "your-order-id",
    "method": "tosspayBill"
}'
```

<br>

### Bid Status Inquiry Request Parameter

```bash
POST /v1/subscribe/{bid}/status   
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter     | Type   | Required | Bytes | Description |
|:--------------|:------:|:--------:|:------:|:-----------|
| `orderId`       | String |  Yes       |  64   | Your unique order ID |
| `method`        | String |  Yes       |  20   | Payment method the token was issued under<br>Currently only `tosspayBill` is supported |
| `ediDate`       | String |     Conditional     |   -   | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `signData`      | String |     No     |  256  | Forgery Verification Data<br>Rule : hex(sha256(orderId + bid +   ediDate + SecretKey))|
| `returnCharSet` | String |     No     |  10   | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |

<br>

### Bid Status Inquiry Response Parameter

```bash
POST
Content-type: application/json
```

| Parameter | Type | Required | Bytes | Description |
|:----------|:----:|:--------:|:------:|:-----------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `tid` | String | Yes | 30 | NICEPAY transaction ID |
| `orderId` | String | Yes | 64 | Your Unique order ID |
| `bid`        | String | Yes  | 30   | Token |
| `bidStatus`  | String | No   | -    | Raw status value from the card network<br>0: in use, 1: suspended, 2: other (undefined by the card network)<br>*Not returned if the card network omits it |
| `status`     | String | Yes  | -    | Interpreted status<br>active: `bidStatus` is 0<br>inactive: `bidStatus` is 1<br>unknown: `bidStatus` is 2, or not returned |

<br>

- Related error codes: `U100`, `U119`, `U309`, `U312`, `U313`, see [API Response code](../code/nicepay-code.md#api-response-code).

<br>
