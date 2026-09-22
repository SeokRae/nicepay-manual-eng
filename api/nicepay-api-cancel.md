# Cancel

## Cancel / Refund

### Over-view
You can use this API to cancel and refund transactions for which payment (approval) has been completed.  
Card payment will be canceled by sending `POST` data with tid (Transaction ID) to the cancel API `/v1/payments/{tid}/cancel`.  
However, for cash transactions such as virtual accounts, refund account information must be passed to the cancel API.  

<br>

<img alt="Sequence diagram of the full card/easy-pay payment cycle for a cardAndEasyPay checkout, from order to cancellation. Steps 8-9 (cancellation, shown in orange: merchant server calls the Cancel API, NicePay responds with resultCode 0000, or a failing cancellation with resultCode other than 0000) are the focus here; steps 1-7 (checkout) are detailed in api/nicepay-api-payment-window-url.md" src="../image/payment-checkout-cancel-cycle.svg" width="800px">

**Steps 8-9 above (in orange)** are this server-to-server call the merchant makes after a card or easy-pay checkout has already completed (see [Card and Easy Pay checkout flow](./nicepay-api-payment-window-url.md#card-and-easy-pay-checkout-flow) for steps 1-7); there's no customer/browser step here. `refundAccount`/`refundBankCode`/`refundHolder` only apply to virtual account refunds and are omitted above; see the parameter tables below for the full field list.

<br>

**Before you start**, you'll need:
- A [Client and Secret key](../info/nicepay-info-key.md) issued from the NicePay admin console
- An `Authorization` header built from those keys, see [Basic and Bearer authentication](../info/nicepay-info-basic-token.md)
- We recommend testing against the [Sandbox](../info/nicepay-info-sandbox.md) first, then switching to Live once verified. The example below uses the Live domain (`api.nicepay.co.kr`); swap it for `sandbox-api.nicepay.co.kr` to test in Sandbox.

<br>

### Cancel with session id example

```bash
curl --location 'https://api.nicepay.co.kr/v1/payments/checkout/641d555b91ae1/cancel' \
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
| Credit card payment   | O          | O                    |             |                | within 1 year       |
| Virtual account (after issuance) | O |                    |             |          | Before deposit expiration |
| Virtual account (after deposit) |  |                      | O           | O              | 180 days            |
| Cash receipt          | not via this API | not via this API |             |                | 1 year after issued |
| Escrow (before registration for shipping) | O |           |             |                | -                   |
| Escrow (after boarding registration) | O |                |             |                | -                   |
| Escrow (after purchasing decision) | not allowed | not allowed | not allowed  | not allowed | -                |
| Escrow (after purchase rejection) | O  |                  | O           |                | -                   |

> Cancelling a cash-receipt payment through `/v1/payments/{tid}/cancel` or `/v1/payments/checkout/{sessionId}/cancel` fails with [`U106`](../code/nicepay-code.md#api-response-code) ("cash receipt cancellation requires a separate API"). That separate cash-receipt cancellation API is not part of the Untact v1 API this manual documents; [open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) if you need it.  

<br>

> #### ⚠️ Important  
> If the virtual account refund request is successful, it will be refunded as of D+1 17:00 on the business day.

<br><br>

### Cancel Request parameter (with sessionId)

```bash
POST /v1/payments/checkout/{sessionId}/cancel  
HTTP/1.1  
Host: api.nicepay.co.kr  
Authorization: Basic <credentials>  or Bearer <token>  
Content-type: application/json;charset=utf-8  
```

| Parameter     | Type      | Required | Bytes | Description |
|:--------------|:---------:|:--------:|:------:|:-----------|
| reason        | String    | O*    | 100       | Cancellation reason<br>*Not required when `isNetCancel` is `true` |
| orderId       | String    | O*    | 64        | Your unique order ID <br>-In case of partial cancellation, orderId cannot be reusable<br>*Not required when `isNetCancel` is `true` |
| cancelAmt     | Int       |       | 12        | Cancellation Amount<br>If the value is missing, full cancellation will be occured |
| mallReserved  | String    |       | 500       | Spare field for store information delivery |
| ediDate      | String    |       | -         | Full Text Creation Date<br>ISO 8601 Format |
| signData      | String    |       | 256       | Forgery Verification Data<br>Rule: hex(sha256(tid + ediDate + SecretKey)) |
| returnCharSet | String    |       | 10        | utf-8(Default) / euc-kr |
| isNetCancel   | Boolean   |       | 5         | `true` sends this as a net cancel, the reversal to use when your server never received the authorization result. See the callout below. Default `false` |
| taxFreeAmt    | Int       |       | 12        | Tax-free amount among cancellation amount |
| refundAccount | String    |       | 16        | Refund account number (Only for Virtual account) |
| refundBankCode| String    |       | 3         | Refund account code (Only for Virtual account) |
| refundHolder  | String    |       | 10        | Refund account holder name (Only for Virtual account) |

> #### ⚠️ Important  
> Use `isNetCancel: true` only for a **net cancel**: your server sent an authorization request and never got a usable answer back, for example a read timeout, so you do not know whether the payment went through. See [Timeout Information](../info/nicepay-info-firewall-timeout.md#timeout-information) for the timeouts that lead here.  
> A net cancel takes a different path from an ordinary cancellation. `reason` and `orderId` are not required, because after a timeout you have no confirmed transaction to describe, and `cancelAmt` is ignored: a net cancel always reverses the whole authorization. NicePay looks the authorization up by `tid`, so if none was ever recorded you get [`U122`](../code/nicepay-code.md#api-response-code) ("No transaction to cancel") rather than a silent success.  
> For a cancellation you are choosing to make on a payment you know succeeded, leave `isNetCancel` out and send `reason` and `orderId` as usual.  

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
| reason        | String    | O*    | 100       | Cancellation reason<br>*Not required when `isNetCancel` is `true` |
| orderId       | String    | O*    | 64        | Your unique order ID <br>-In case of partial cancellation, orderId cannot be reusable<br>*Not required when `isNetCancel` is `true` |
| cancelAmt     | Int       |       | 12        | Cancellation Amount<br>If the value is missing, full cancellation will be occured |
| mallReserved  | String    |       | 500       | Spare field for store information delivery |
| ediDate      | String    |       | -         | Full Text Creation Date<br>ISO 8601 Format |
| signData      | String    |       | 256       | Forgery Verification Data<br>Rule: hex(sha256(tid + ediDate + SecretKey)) |
| returnCharSet | String    |       | 10        | utf-8(Default) / euc-kr |
| isNetCancel   | Boolean   |       | 5         | `true` sends this as a net cancel, the reversal to use when your server never received the authorization result. See the callout below. Default `false` |
| taxFreeAmt    | Int       |       | 12        | Tax-free amount among cancellation amount |
| refundAccount | String    |       | 16        | Refund account number (Only for Virtual account) |
| refundBankCode| String    |       | 3         | Refund account code (Only for Virtual account) |
| refundHolder  | String    |       | 10        | Refund account holder name (Only for Virtual account) |

<br><br>


### Response parameter (for tid or sessionId cancel)

```bash
POST
Content-type: application/json
```

| Parameter | Type | Required | Bytes | Description |
|:----------|:----:|:--------:|:------:|:-----------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| tid | String | O | 30 | NICEPAY transaction ID |
| cancelledTid | String | | 30 | Cancellation transaction ID<br>- Responded only with cancellation requests<br>- Use when finding canceled transaction information in the cancels object. |
| orderId | String | O | 64 | Unique order number |
| sessionId | String | | - | Checkout session ID associated with this transaction, when available |
| ediDate | String | O | - | Response message creation date and time (ISO 8601 format) |
| signature | String | | 256 | Forgery verification data<br>- Respond only to valid transactions<br>- Creation rule: hex(sha256(tid + amount + ediDate+ SecretKey))<br>- For data validation, it is recommended to implement a comparison at business logic |
| status | String | O | 20 | Payment processing status<br>paid: payment completed<br> ready: ready<br>failed: payment failed<br>cancelled: cancelled<br>partialCancelled: partially cancelled<br>['paid', 'ready', 'failed', 'cancelled', 'partialCancelled'] |
| paidAt | String | O | - | Time of payment completed ISO 8601 format<br>If payment is not completed, return 0 |
| failedAt | String | O | - | Time of payment failure ISO 8601 format<br>If not payment is not failed, return 0 |
| cancelledAt | String | O | - | Payment cancellation time ISO 8601 format<br>If it is not cancellation request, return 0<br>In case of partial cancellation, the last cancellation time will be return |
| payMethod | String | O | 10 | Payment method<br><br>card: credit card, <br>vbank: virtual account, <br>bank: account transfer, <br>cellphone: mobile phone, <br>naverpay=Naver Pay, <br>kakaopay=Kakao Pay, <br>samsungpay=Samsung Pay, <br>payco=Payco, <br>ssgpay=SSG Pay, <br>tosspay=Toss Pay |
| amount | Int | O | 12 | payment amount |
| balanceAmt | Int | O | 12 | Remained balance for cancellation |
| goodsName | String | O | 40 | Product name |
| mallReserved | String | | 500 | Spare field for store information delivery<br>It is recommended to use JSON string format.<br>However, double quotation marks (") cannot be used |
| useEscrow | Boolean | O | - | Escrow transaction status<br> true: Escrow transaction |
| currency | String | O | 3 | Approved currency<br>KRW: Korean Won, USD: USD, CNY: Yuan |
| channel | String | | 10 | pc:PC payment, mobile:mobile payment<br>['pc', 'mobile', 'null'] |
| approveNo | String | | 30 | Authorization Number<br>Credit Card, Bank Transfer, Mobile Phone<br>Only populated when cancelling with `tid`; always `null` when cancelling with `sessionId` |
| buyerName | String | | 30 | Buyer name |
| buyerTel | String | | 40 | Buyer phone number |
| buyerEmail | String | | 60 | Buyer Email |
| issuedCashReceipt | Boolean | | - | Issuance status of cash receipts<br>true: issued / false: not issued |
| receiptUrl | String | | 200 | URL for receipt|
| mallUserId | String | | 20 | User ID managed by the store |
| messageSource | String | |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|

<br>

#### Coupon information <img alt="Object type" src="https://img.shields.io/badge/-Object-yellow"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter |           |   Type   |  Required   |  Bytes  |    Description     |
|:----------|:----------|:--------:|:-----:|:-------:|:--------------|
| coupon    |           | Object   |       | - | Information for instant discount promotion |
|           | couponAmt | Int      |       | 12 | Amount of instant discount applied |

<br>

#### Card information <img alt="Object type" src="https://img.shields.io/badge/-Object-yellow"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter |                |   Type   |  Required   |  Bytes  | Description   |
|:----------|:---------------|:--------:|:-----:|:-------:|:------------------|
| card | | Object | | | Credit Card Object |
| | cardCode | String | O | 3 | Card company code |
| | cardName | String | O | 20 | Card issuer name <br> ex) BC |
| | cardNum | String | | 20 | Card number<br>3rd range masked<br>Ex) 53611234****1234*<br>- Kakao Money/Naver Point/Payco Point used for payment 'null' will be return. |
| | cardQuota | Int | O | 3 | Installment Month<br>0: lump sum, 2:2 months, 3:3 months … |
| | isInterestFree | Boolean | O | - | The store pays the customer's installment interest<br>true:yes, false:no |
| | cardType | String | | 1 | Card type<br>credit:credit card, check:debit |
| | canPartCancel | Boolean | | - | Whether partial cancellation is possible<br>true: Possible, false: Impossible, null: not reported by the acquirer for this card |
| | acquCardCode | String | O | 3 | Acquirer code |
| | acquCardName | String | O | 100 | Acquirer Name |

<br>

#### Cash receipts information <img alt="Array type" src="https://img.shields.io/badge/-Array-blueviolet"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter     |              |   Type   |   Required   |  Bytes  | Description |
|:--------------|:-------------|:--------:|:------:|:------:|:-------------------|
| cashReceipts | | Array | | | Cash Receipt Issuance Information<br>-When the customer used Naverpay Points and Virtual Account, this value will be return.<br>-In case of partial cancellation, array will be more than 2 |
| | receiptTid | String | O | 30 | Cash Receipt TID |
| | orgTid | String | O | 30 | Related original approval/cancel transaction ID.<br>In case of partial cancellation, it is mapped with the original TID. |
| | status | String | O | 20 | issueRequested : Issuance Requested <br>issueReqCancelled : Issuance Request cancelled<br>issued: Issuance completed by the National Tax Service <br>issueFailed: Issuance failed<br>cancelRequested: Cancellation requested <br>cancelReqCancelled: issuance Cancelled by the National Tax Service<br>cancelled: Cancellation completed <br>cancelFailed: Cancellation failed |
| | amount | Int | O | 12 | Total amount of cash receipt issued |
| | taxFreeAmt | Int | O | 12 | Tax-free amount of the cash receipt |
| | receiptType | String | O | 20 | Cash Receipt Type<br>individual: For personal income deduction<br>company: For proof of business expenses |
| | issueNo | String | O | 30 | Issued number by the National Tax Service |
| | receiptUrl | String | O | 200 | Cash Receipt URL for user |

<br>

#### Bank transfer information <img alt="Object type" src="https://img.shields.io/badge/-Object-yellow"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter  |           |   Type   |  Required   |  Bytes  | Description  |
|:-----------|:----------|:--------:|:-----:|:------:|:------------------|
| bank       | 　         |  Object  |   　   |   　    | bank object    |
| 　          | bankCode  |  String  |   O   |   3    | bank code  |
|            | bankName  |  String  |   O   |   20   | bank name (euc-kr encoded) |

<br>

#### Virtual Account information <img alt="Object type" src="https://img.shields.io/badge/-Object-yellow"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter |              |  Type   |  Required   |  Bytes  | Description  |
|:----------|:-------------|:-------:|:-----:|:------:|:-------------------------|
| vbank | | Object | | | Virtual account object |
| | vbankCode | String | O | 3 | Virtual account bank code |
| | vbankName | String | O | 20 | Virtual account bank name |
| | vbankNumber | String | O | 20 | Virtual account number |
| | vbankExpDate | String | O | - | Expiration Date<br>ISO 8601 format |
| | vbankHolder | String | O | 40 | Virtual account holder name |

<br>

#### Cancel information <img alt="Array type" src="https://img.shields.io/badge/-Array-blueviolet"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter |             |  Type   |  Required   |  Bytes  | Description  |
|:----------|:------------|:-------:|:-----:|:-------:|:-----------------------|
| cancels | | Array | | | Cancellation history |
| | tid | String | O | 30 | Cancel Transaction ID |
| | amount | Int | O | 12 | Cancellation Amount |
| | cancelledAt | String | O | - | Canceled Time<br>ISO 8601 format |
| | reason | String | O | 100 | Cancellation reason |
| | receiptUrl | String | O | 200 | <br>Receipt URL for user n |
| | couponAmt | Int | | 12 | Cancellation amount of coupon <br> *Optional|

<br>