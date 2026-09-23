# Transaction Status Inquiry

This page covers these APIs. All of them are available in Sandbox, and the two card inquiries return dummy data there.

- [Transaction Status Inquiry](#transaction-status-inquiry-with-tidtransaction-id): the full record of one payment, including its cancellations. Look it up by `tid` when you stored it. Use `orderId` when your server lost the payment result before it stored `tid`, and `sessionId` for a Checkout session.
- [Check Authorization Amount](#check-authorization-amount): only whether an `amount` matches the approved amount of a `tid`.
- [Card event API](#card-event-api) and [Interest-free installment information API](#interest-free-installment-information-api): card company promotions to show the customer before the payment.

## Check Transaction Status 

<a href="../image/payment-retrieve.svg"><img alt="Sequence diagram of four calls the Merchant Server can make: Check Authorization Amount, or Transaction Status Inquiry by tid, orderId, or sessionId" src="../image/payment-retrieve.svg" width="800px"></a>

You can use the Transaction Status Inquiry API to check information about the success or failure of a payment (approval) request.

It is recommended to use the Transaction Status Inquiry API in the following cases.
- When your server did not receive the result of a payment request, for example after a timeout. See [Timeout Information](../info/nicepay-info-firewall-timeout.md#timeout-information) for which identifier to look up for each integration.
- In case of suspicion of forgery and alteration of data in the payment (approval).
- When it is necessary to check the cancellation balance of a payment (approval).

We recommend testing against the [Sandbox](../info/nicepay-info-sandbox.md) first, then switching to Live once verified. The examples below call Sandbox (`sandbox-api.nicepay.co.kr`) with the public Sandbox key from [Test key information](../info/nicepay-info-sandbox.md#test-key-information). For Live, use `api.nicepay.co.kr` and your own Live key.

<br>

### Check Transaction Status Example code

```bash
curl -X GET 'https://sandbox-api.nicepay.co.kr/v1/payments/nicuntct1m0101210727200125A056' \
-H 'Content-Type: application/json' \
-H 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM='
```

<br>

## Check Authorization Amount

- If you need to check the payment amount after approval, use the `Check-amount` API to check the approved payment amount.
- `resultCode` is `0000` whether or not the amounts match, so check `isValid`. `false` means that the `amount` you sent differs from the amount NicePay approved for the `tid`. NicePay compares with the amount of the original approval, not with the balance after partial cancellations.
- If `isValid` is `false`, cancel the payment before you deliver the goods: send [Cancel request with tid](./nicepay-api-cancel.md#cancel-request-parameter-with-tid) without `cancelAmt` to cancel the full amount.

> **⚠️ Important:** You are responsible for problems caused by not checking the approved (payment) amount.

<br>

### Check Authorization Amount Example code

```bash
curl -X POST 'https://sandbox-api.nicepay.co.kr/v1/check-amount/nicuntct1m0101210727200708A058' \
-H 'Content-Type: application/json' \
-H 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
--data '{
    "amount" : 1004
}'
```

<br>

### Check Authorization Amount Request parameter

```bash
POST /v1/check-amount/{tid}  
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

Required: Yes = always send; No = optional; Conditional = send in the case stated in Description. Bytes = maximum length in bytes.

| Parameter     | Type   | Required | Bytes | Description |
|:--------------|:------:|:--------:|:------:|:---------|
| `amount`      |  Int   | Yes |   12   | Amount to compare with the approved amount<br>Whole number with no decimal point, in the same unit as the `amount` that NicePay returns for the payment. See [Amounts and currencies](../info/nicepay-info-general.md#amounts-and-currencies) |
| `ediDate`     | String | Conditional |   -    | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `signData`    | String | No |  256   | Forgery Verification Data<br>Rule: hex(sha256(tid + amount + ediDate + SecretKey)) |
| `returnCharSet` | String | No | 10        | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |

<br>

### Check Authorization Amount Response parameter

```bash
POST
Content-type: application/json
```

Dates and times that NicePay returns are in Korea Standard Time (KST, UTC+9), for example `2023-03-24T14:04:16.982+0900`. See [Dates in responses](../info/nicepay-info-general.md#dates-in-responses) for how to parse them.

Required: Yes = has a non-empty value in every response whose `resultCode` is `0000`; No = can be `null`, empty, or left out. For a field of an object or array, Yes applies whenever that object or array element is present.

| Parameter     | Type   | Required | Bytes | Description |
|:-----------|:-----:|:-----:|:------:|:-------------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `ediDate` | String | Yes | - | Message creation date and time (ISO 8601 format) |
| `signature`  |  String  |   Yes   |  256   | Forgery Verification Data<br>Rule: hex(sha256(tid + ediDate + SecretKey)) |
| `isValid`  |   Boolean   |   Yes   |   -    | Whether the amount transferred to the check-amount API matches the actual approved amount.<br>true : match / false : mismatch |
| `tid`      |   String   |   Yes   |   30   | Requested transaction ID |


<br><br>

### Transaction Status Inquiry (with tid:Transaction ID)

```bash
GET /v1/payments/{tid} 
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

`GET` requests have no body. Send `ediDate`, `signData` and `returnCharSet` as query parameters, and percent-encode each value. The `+` in the `ediDate` offset must become `%2B`: an unencoded `+` reaches NicePay as a space, and the `signData` check then fails with [`U312`](../code/nicepay-code.md#api-response-code). This is the same in Sandbox and Live. When you do not send `signData`, you can leave `ediDate` out too, as in [Check Transaction Status Example code](#check-transaction-status-example-code).

Example with the Sandbox test key from [Test key information](../info/nicepay-info-sandbox.md#test-key-information):

```bash
tid       = UT0000104m00012303241646422011
ediDate   = 2023-03-24T16:55:00.000+0900
SecretKey = 13e969a77a0545799242ccc3915243d3

String to hash (before encoding):
UT0000104m000123032416464220112023-03-24T16:55:00.000+090013e969a77a0545799242ccc3915243d3

signData:
c78d6b12b11036bc626a018d26320aa04ba0344e9c52fd0303ef23377ca3569b

curl -X GET 'https://sandbox-api.nicepay.co.kr/v1/payments/UT0000104m00012303241646422011?ediDate=2023-03-24T16%3A55%3A00.000%2B0900&signData=c78d6b12b11036bc626a018d26320aa04ba0344e9c52fd0303ef23377ca3569b' \
-H 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM='
```

| Parameter | Type | Required | Bytes | Description |
|:--------------|:-----:|:-----:|:-----:|:----------|
| `ediDate` | String | Conditional | - | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `signData`    |   String   | No |  256  | Forgery Verification Data<br>Generation rule: hex(sha256(tid + ediDate + SecretKey))|
| `returnCharSet` | String    | No | 10        | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |

<br><br>

### Transaction Status Inquiry (with orderId)

```bash
GET /v1/payments/find/{orderId}   
HTTP/1.1    
Host: api.nicepay.co.kr  
Authorization: Basic <credentials>  or Bearer <token>  
Content-type: application/json;charset=utf-8  
```

Send `ediDate`, `signData` and `returnCharSet` as query parameters, as in [Transaction Status Inquiry (with tid:Transaction ID)](#transaction-status-inquiry-with-tidtransaction-id).

| Parameter | Type | Required | Bytes | Description |
|:--------------|:-----:|:-----:|:-----:|:----------|
| `ediDate` | String | Conditional | - | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `signData`    |   String   | No |  256  | Forgery Verification Data<br>Rule: hex(sha256(orderId + ediDate + SecretKey))|
| `returnCharSet` | String    | No | 10        | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |

<br>

### Transaction Status Inquiry (with sessionId)

```bash
GET /v1/payments/checkout/{sessionId}  
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>  
Content-type: application/json;charset=utf-8  
```

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| `sessionId` | String  | Yes | 256	  | Merchant unique session id, issued by merchant | 

<br>

### Response parameter (Transaction Status Inquiry by tid, orderId, sessionId)

```bash
Content-type: application/json
```

| Parameter | Type | Required | Bytes | Description |
|:------------------|:--------:|:-----:|:------:|:---------------------------------------------------------------------------------------------------------------------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `tid` | String | Yes | 30 | NICEPAY transaction ID |
| `cancelledTid` | String | No | 30 | Cancellation transaction ID<br>- Responded only with cancellation requests<br>- Use when finding canceled transaction information in the cancels object. |
| `orderId` | String | Yes | 64 | Unique order number |
| `sessionId` | String | No | 256 | Checkout session ID of the payment<br>Always returned when you look up with `sessionId`. When you look up with `tid` or `orderId`, returned only when the payment was made through Checkout; otherwise the key is left out |
| `ediDate` | String | Yes | - | Response message creation date and time (ISO 8601 format) |
| `signature` | String | Yes | 256 | Forgery verification data<br>Rule: hex(sha256(tid + amount + ediDate + SecretKey)), see [Verifying the payment result](./nicepay-api-payment-window-url.md#verifying-the-payment-result)<br>Covers only `tid`, `amount` and `ediDate`. Check `resultCode` and `status` separately<br>Every inquiry returns a new `ediDate`, so `signature` differs on each call |
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
| `approveNo` | String | No | 30 | Authorization Number<br>Credit Card, Bank Transfer, Mobile Phone<br>Only populated when you look up with `tid` or `orderId`; always `null` when you look up with `sessionId` |
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
| `coupon`  |           | Object   |   No    | - | Information for instant discount promotion |
|           | `couponAmt` | Int      |   Yes    | 12 | Amount of instant discount applied |

<br>

#### Card information <img alt="Object type" src="https://img.shields.io/badge/-Object-F7DF1E"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-555555">

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
| `bank`     |          |  Object  |  No  |       | bank object    |
|           | `bankCode`  |  String  |   Yes   |   3    | bank code  |
|            | `bankName`  |  String  |   Yes   |   20   | Bank name |

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

<br><br>

## Card event API

### Card event API Over-view
The card event API responds with event information for each card company corresponding to the requested amount.
Use it to show customers which card company to choose.

> If the amount is less than KRW 50,000, No interest will be return.
> In Sandbox, this API always returns the same fixed dummy card-event data regardless of the amount you send; it does not simulate real card company data.

<br>

### Card event API Example code

```bash
curl -X GET 'https://sandbox-api.nicepay.co.kr/v1/card/event?amount={your-amount}&useAuth=false&ediDate={ISO 8601}&...' \
-H 'Content-Type: application/json' \
-H 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM='
```

<br>

### Card event API Request parameter

```bash
GET /v1/card/event?amount={your-amount}&useAuth=false&ediDate={ISO 8601 string}&...   HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

Send the parameters below as query parameters, and percent-encode each value, as in [Transaction Status Inquiry (with tid:Transaction ID)](#transaction-status-inquiry-with-tidtransaction-id).

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| `amount` | Int | Yes | 12 | payment amount |
| `useAuth`     |  Boolean   | Yes |   -   | true : checkout or payment window <br> false : billing or key-in  |
| `ediDate`    | String    | Conditional | -         | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `mid`         |  String   | No |  10   | [Optional] Merchant ID separately contracted with Nice Payments |
| `signData`    | String    | No | 256       | Forgery Verification Data<br>Generation rule: hex(sha256(ediDate + SecretKey)) |
| `returnCharSet` | String    | No | 10        | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |

<br>

### Card event API Response parameter

```bash
POST
Content-type: application/json
```

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `ediDate`  | String    | Yes | -         | Full Text Creation Date<br>ISO 8601 Format |
| `signature`  | String  |  Yes  |  256   | Forgery Verification Data<br>Generation rule: hex(sha256(ediDate + SecretKey))  |
| `cardPoint`  | String  |  No  |       | Cards that support point payment<br>-List card codes with a colon (:) as separator<br>-Card company points provide usable card company information regardless of the amount<br>ex) 01:02:04:07<br>- Description: Card company points can be used for BC, Kookmin, Samsung, and Hyundai cards|


#### Card event API Interest-free installment information <img alt="Array type" src="https://img.shields.io/badge/-Array-blueviolet">

| Parameter     | Field           |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----------------|:--------:|:-----:|:------:|:---------|
| `interestFree` |                 |  Array   |  No  |   -    | All interest-free installment information<br>- All interest-free information provided by NICEPAY and interest-free information applied to the store will be answered.<br>- The interest-free information object for each card code is answered.  |
| | `cardCode` | String | Yes | 3 | Card company code |
| | `cardName` | String | Yes | 20 | Card issuer name <br> ex) BC |
| | `freeInstallment` |  String  |   Yes   |  200   | Interest-free installment months<br>Separator is a colon (:)<br>ex) 02:03:04:05<br>-Interest-free installments for 2,3,4,5 months |

<br>

## Interest-free installment information API

### Interest-free installment information API Over-view
Interest-free installment information API can check interest-free about card companies and amount range.

> In Sandbox, this API always returns the same fixed dummy interest-free data; it does not simulate real card company data.

<br>

### Interest-free installment information API Example code

```bash
curl -X GET 'https://sandbox-api.nicepay.co.kr/v1/card/interest-free?useAuth=true&ediDate={ISO 8601 format date}' \
-H 'Content-Type: application/json' \
-H 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM='
```

<br>

### Interest-free installment information Request parameter

```bash
GET /v1/card/interest-free?useAuth=false&ediDate={..} 
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

Send the parameters below as query parameters, and percent-encode each value, as in [Transaction Status Inquiry (with tid:Transaction ID)](#transaction-status-inquiry-with-tidtransaction-id).

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:-----:|:------:|:--------|
| `useAuth`     |  Boolean   | Yes |   -   | true : checkout or payment window <br> false : billing or key-in  |
| `ediDate`  | String    | Conditional | -         | Request timestamp (ISO 8601) that your Merchant Server creates, see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)<br>Required when you send `signData` |
| `mid`         |  String   | No |  10   | [Optional] Merchant ID separately contracted with Nice Payments |
| `signData`    | String    | No | 256       | Forgery Verification Data<br>Generation rule: hex(sha256(ediDate + SecretKey)) |
| `returnCharSet` | String    | No | 10        | `utf-8` (default) or `euc-kr`<br>Sets the charset in the `Content-Type` header of the response. Keep `utf-8` |

<br>

### Interest-free installment information Response parameter

```bash
Content-type: application/json
```

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `ediDate`  | String    | Yes | -         | Full Text Creation Date<br>ISO 8601 Format |
| `signature`  | String  |  Yes  |  256   | Forgery Verification Data<br>Generation rule: hex(sha256(ediDate + SecretKey))  |



#### Interest-free installment information <img alt="Array type" src="https://img.shields.io/badge/-Array-blueviolet">

| Parameter    | Field           | Subfield     |   Type   |  Required   |  Bytes  | Description  |
|:-------------|:----------------|:-------------|:--------:|:-----:|:------:|:-------------------|
| `interestFree` |                 |              |  Array   |  No  |   -    | All interest-free installment information<br>- All interest-free information provided by NICEPAY and interest-free information applied to the store will be answered.<br>- The interest-free information object for each card code is answered.  |
|              | `cardCode`      |              |  String  |   Yes   |   3    | Card company code   |
|              | `cardName`      |              |  String  |   Yes   |   20   | Card issuer name <br> ex) BC  |
|              | `freeInstallment` |              |  Array   |   Yes   |   -    | Interest-free installment information |
|              |                 | `minAmt`     |   Int    |   Yes   |   12   | Minimum amount for event<br>ex) 50000                                                                       |
|              |                 | `maxAmt`     |   Int    |   Yes   |   14   | Maximum amount for event<br>ex) 99999999999999                                                              |
|              |                 | `installment`  |  String  |   Yes   |  100   | Interest-free installment months<br>Separator is a colon (:)<br>ex) 02:03:04:05<br>-Interest-free installments for 2,3,4,5 months   |
|              |                 | `eventToDate`  |  String  |   Yes   |   -    | Event deadline<br>ISO 8601 format                                                                        |

<br>