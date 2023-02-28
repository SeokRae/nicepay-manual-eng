# Retrieve

## Retrieve a transaction

<img src="../image/payment-retrieve.svg" width="800px"> 

Retrieve API can be used if you need to check information according to the success or failure of a 💳 payment (approval) request.

It is recommended to use the Retrieve API in the following cases.
- If timeout of `Http-client` occurs after calling the payment (approval) API, you can search the transaction through Retrieve API with `orderId`.
- In case of suspicion of forgery and alteration of data in the payment (approval).
- When it is necessary to check the cancellation balance of a payment (approval).

<br>

### Example code

```bash
curl -X GET 'https://api.nicepay.co.kr/v1/payments/nicuntct1m0101210727200125A056' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic YWYwZDExNjIzNmRmNDM3ZjgzMT...'
```

<br>
## Check amount API

- If you need to check the payment amount after approval, use the `Check-amount` API to check the 💳 approved payment amount.
- If the amount requested and the approval amount are different, be sure to cancel the payment.

> ⚠️ IMPORTANT
> 💳 Merchants are responsible for problems caused by not checking the approved (payment) amount.

<br>

## Check amount API 

### Example code

```bash
curl -X POST 'https://api.nicepay.co.kr/v1/check-amount/nicuntct1m0101210727200708A058' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic YWYwZDExNjIzNmRmNDM3Zjgz...' 
-D '{
    "amount" : 1004
}' 
```

<br>

### Check amount API Request parameter

```bash
POST /v1/check-amount/{tid}  
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter     | Type   | Required | Bytes | Description |
|:--------------|:------:|:--------:|:------:|:---------|
| amount        |  Int   |   O   |   12   | amount of payment |
| ediDate       | String |   　   |   -    | Message creation date and time (ISO 8601 format) |
| signData      | String |   　   |  256   | Forgery Verification Data<br>Rule: hex(sha256(tid + amount + ediDate + SecretKey)) |
| returnCharSet | String |       | 10        | utf-8(Default) / euc-kr |

<br>

### Check amount API Response parameter

```bash
POST
Content-type: application/json
```

| Parameter     | Type   | Required | Bytes | Description |
|:-----------|:-----:|:-----:|:------:|:-------------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| ediDate | String | O | - | Message creation date and time (ISO 8601 format) |
| signature  |  String  |   O   |  256   | Forgery Verification Data<br>Rule: hex(sha256(tid + ediDate + SecretKey)) |
| isValid    |   B   |   O   |   -    | Whether the amount transferred to the check-amount API matches the actual approved amount.<br>true : match / false : mismatch |
| tid        |   S   |   O   |   30   | Requested transaction ID |

<br><br>

### Retrieve a transaction (with tid:transaction ID)

```bash
GET /v1/payments/{tid} 
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```
| Parameter | Type | Required | Bytes | Description |
|:--------------|:-----:|:-----:|:-----:|:----------|
| ediDate | String | O | - | Response message creation date and time (ISO 8601 format) |
| signData      |   String   |   　   |  256  | Forgery Verification Data<br>Generation rule: hex(sha256(tid + ediDate + SecretKey))|
| returnCharSet | String    |       | 10        | utf-8(Default) / euc-kr |

<br><br>

### Retrieve a transaction (with orderId)

```bash
GET /v1/payments/find/{orderId}  
HTTP/1.1    
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter | Type | Required | Bytes | Description |
|:--------------|:-----:|:-----:|:-----:|:----------|
| orderDate     |  String   |  O   |   8   | order date<br>YYYYMMDD |
| ediDate | String | O | - | Message creation date and time (ISO 8601 format) |
| signData      |   String   |   　   |  256  | Forgery Verification Data<br>Rule: hex(sha256(tid + ediDate + SecretKey))|
| returnCharSet | String    |       | 10        | utf-8(Default) / euc-kr |

<br>

## Retrieve a transaction Response parameter

```bash
Content-type: application/json
```

| Parameter | Type | Required | Bytes | Description |
|:------------------|:--------:|:-----:|:------:|:---------------------------------------------------------------------------------------------------------------------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| tid | String | O | 30 | NICEPAY transaction ID |
| canceledTid | String | | 30 | Cancellation transaction ID<br>- Responded only with cancellation requests<br>- Use when finding canceled transaction information in the cancels object. |
| orderId | String | O | 64 | Unique order number |
| ediDate | String | O | - | Response message creation date and time (ISO 8601 format) |
| signature | String | | 256 | Forgery verification data<br>- Respond only to valid transactions<br>- Creation rule: hex(sha256(tid + amount + ediDate+ SecretKey))<br>- For data validation, it is recommended to implement a comparison at business logic |
| status | String | O | 20 | Payment processing status<br>paid: payment completed<br> ready: ready<br>failed: payment failed<br>canceled: canceled<br>partialCancelled: partially canceled<br>expired: expired<br>['paid', 'ready', 'failed', 'cancelled', 'partialCancelled', 'expired'] |
| paidAt | String | O | - | Time of payment completed ISO 8601 format<br>If payment is not completed, return 0 |
| failedAt | String | O | - | Time of payment failure ISO 8601 format<br>If not payment is not failed, return 0 |
| canceledAt | String | O | - | Payment cancellation time ISO 8601 format<br>If it is not cancellation request, return 0<br>In case of partial cancellation, the last cancellation time will be return |
| payMethod | String | O | 10 | Payment method<br><br>card: credit card, <br>vbank: virtual account, <br>bank: account transfer, <br>cellphone: mobile phone, <br>naverpay=Naver Pay, <br>kakaopay=Kakao Pay, <br>samsungpay=Samsung Pay |
| amount | Int | O | 12 | payment amount |
| balanceAmt | Int | O | 12 | Remained balance for cancellation |
| goodsName | String | O | 40 | Product name |
| mallReserved | String | | 500 | Spare field for store information delivery<br>It is recommended to use JSON string format.<br>However, double quotation marks (") cannot be used |
| useEscrow | Boolean | O | - | Escrow transaction status<br> true: Escrow transaction |
| currency | String | O | 3 | Approved currency<br>KRW: Korean Won, USD: USD, CNY: Yuan |
| channel | String | | 10 | pc:PC payment, mobile:mobile payment<br>['pc', 'mobile', 'null'] |
| approveNo | String | | 30 | Authorization Number<br>Credit Card, Bank Transfer, Mobile Phone |
| buyerName | String | | 30 | Buyer name |
| buyerTel | String | | 40 | Buyer phone number |
| buyerEmail | String | | 60 | Buyer Email |
| issuedCashReceipt | Boolean | | - | Issuance status of cash receipts<br>true: issued / false: not issued |
| receiptUrl | String | | 200 | URL for receipt|
| mallUserId | String | | 20 | User ID managed by the store |

<br>

#### Coupon information <img src="https://img.shields.io/badge/-Object-yellow"> <img src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter |           |   Type   |  Required   |  Bytes  |    Description     |
|:----------|:----------|:--------:|:-----:|:-------:|:--------------|
| coupon    |           | Object   |       | - | Information for instant discount promotion |
|           | couponAmt | Int      |       | 12 | Amount of instant discount applied |

<br>

#### Card information <img src="https://img.shields.io/badge/-Object-yellow"> <img src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter |                |   Type   |  Required   |  Bytes  | Description   |
|:----------|:---------------|:--------:|:-----:|:-------:|:------------------|
| card | | Object | | | Credit Card Object |
| | cardCode | String | O | 3 | Card company code |
| | cardName | String | O | 20 | Card issuer name <br> ex) BC |
| | cardNum | String | | 20 | Card number<br>3rd range masked<br>Ex) 53611234****1234*<br>- Kakao Money/Naver Point/Payco Point used for payment 'null' will be return. |
| | cardQuota | Int | O | 3 | Installment Month<br>0: lump sum, 2:2 months, 3:3 months … |
| | isInterestFree | Boolean | O | - | The store pays the payer's installment interest<br>true:yes, false:no |
| | cardType | String | | 1 | Card type<br>credit:credit card, check:debit |
| | canPartCancel | String | O | - | Whether partial cancellation is possible<br>true: Possible, false: Impossible |
| | acquCardCode | String | O | 3 | Acquirer code |
| | acquCardName | String | O | 100 | Acquirer Name |

<br>

#### Cash receipts information <img src="https://img.shields.io/badge/-Array-blueviolet"> <img src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter     |              |   Type   |   Required   |  Bytes  | Description |
|:--------------|:-------------|:--------:|:------:|:------:|:-------------------|
| cashReceipts | | Array | | | Cash Receipt Issuance Information<br>-When payer used Naverpay Points and Virtual Account, this value will be return.<br>-In case of partial cancellation, array will be more than 2 |
| | receiptTid | String | O | 30 | Cash Receipt TID |
| | orgTid | String | O | 30 | Related original approval/cancel transaction ID.<br>In case of partial cancellation, it is mapped with the original TID. |
| | status | String | O | 20 | issueRequested : Issuance Requested <br>issueReqCancelled : Issuance Request cancelled<br>issued: Issuance completed by the National Tax Service <br>issueFailed: Issuance failed<br>cancelRequested: Cancellation requested <br>cancelReqCancelled: issuance Cancelled by the National Tax Service<br>cancelled: Cancellation completed <br>cancelFailed: Cancellation failed |
| | amount | Int | O | 12 | Total amount of cash receipt issued |
| | taxFreeAmt | Int | O | 12 | Tax-free amount of the cash receipt |
| | receiptType | String | O | 20 | Cash Receipt Type<br>individual: For personal income deduction<br>company: For proof of business expenses |
| | issueNo | String | O | 30 | Issued number by the National Tax Service |
| | receiptUrl | String | O | 200 | Cash Receipt URL for user |

<br>

#### Bank transfer information <img src="https://img.shields.io/badge/-Object-yellow"> <img src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter  |           |   Type   |  Required   |  Bytes  | Description  |
|:-----------|:----------|:--------:|:-----:|:------:|:------------------|
| bank       | 　         |  Object  |   　   |   　    | bank object    |
| 　          | bankCode  |  String  |   O   |   3    | bank code  |
|            | bankName  |  String  |   O   |   20   | bank name (euc-kr encoded) |

<br>

#### Virtual Account information <img src="https://img.shields.io/badge/-Object-yellow"> <img src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter |              |  Type   |  Required   |  Bytes  | Description  |
|:----------|:-------------|:-------:|:-----:|:------:|:-------------------------|
| vbank | | Object | | | Virtual account object |
| | vbankCode | String | O | 3 | Virtual account bank code |
| | vbankName | String | O | 20 | Virtual account bank name |
| | vbankNumber | String | O | 20 | Virtual account number |
| | vbankExpDate | String | O | - | Expiration Date<br>ISO 8601 format |
| | vbankHolder | String | O | 40 | Virtual account holder name |

<br>

#### Cancel information <img src="https://img.shields.io/badge/-Array-blueviolet"> <img src="https://img.shields.io/badge/-nullable-lightgrey">

| Parameter |             |  Type   |  Required   |  Bytes  | Description  |
|:----------|:------------|:-------:|:-----:|:-------:|:-----------------------|
| cancels | | Array | | | Cancellation history |
| | tid | String | O | 30 | Cancel Transaction ID |
| | amount | Int | O | 12 | Cancellation Amount |
| | canceledAt | String | O | - | Canceled Time<br>ISO 8601 format |
| | reason | String | O | 100 | Cancellation reason |
| | receiptUrl | String | O | 200 | <br>Receipt URL for user n |
| | couponAmt | Int | | 12 | Cancellation amount of coupon <br> *Optional|

<br><br>

## Card event API

### Overview
The card event API responds with event information for each card company corresponding to the requested amount.
You can conveniently provide information to user for selecting a credit card company.

> If the amount is less than KRW 50,000, No interest will be return.

<br>

### Example code

```bash
curl -X GET 'https://api.nicepay.co.kr/v1/card/event?amount={your-amount}&useAuth=false&ediDate={ISO 8601}&...' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic YWYwZDExNjIzNmRmNDM3ZjgzMT...'
```

<br>

### Card event API Request parameter

```bash
GET /v1/card/event?amount={your-amount}&useAuth=false&ediDate={ISO 8601 string}&...   HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| amount | Int | O | 12 | payment amount |
| useAuth       |  Boolean   |   O   |   -   | true : checkout or payment window <br> false : billing or key-in  |
| ediDate      | String    |  O | -         | Full Text Creation Date<br>ISO 8601 Format |
| mid           |  String   |   　   |  10   | [Optional] Merchant ID separately contracted with Nice Payments |
| signData      | String    |       | 256       | Forgery Verification Data<br>Generation rule: hex(sha256(ediDate + SecretKey) |
| returnCharSet | String    |       | 10        | utf-8(Default) / euc-kr |

<br>

### Card event API Response parameter

```bash
POST
Content-type: application/json
```

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| ediDate  | String    |   | -         | Full Text Creation Date<br>ISO 8601 Format |
| signature  | String  |   　   |  256   | Forgery Verification Data<br>Generation rule: hex(sha256(ediDate + SecretKey)  |
| cardPoint  | String  |   　   |   　    | Cards that support point payment<br>-List card codes with a colon (:) as separator<br>-Card company points provide usable card company information regardless of the amount<br>ex) 01:02:04:07<br>- Description: Card company points can be used for BC, Kookmin, Samsung, and Hyundai cards|


### Interest-free installment information <img src="https://img.shields.io/badge/-Array-blueviolet">

| Parameter     |                 |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----------------|:--------:|:-----:|:------:|:---------|
| interestFreet |                 |  Array   |       |   -    | All interest-free installment information<br>- All interest-free information provided by NICEPAY and interest-free information applied to the store will be answered.<br>- The interest-free information object for each card code is answered.  |
| | cardCode | String | O | 3 | Card company code |
| | cardName | String | O | 20 | Card issuer name <br> ex) BC |
| | freeInstallment |  String  |   O   |  200   | Interest-free installment months<br>Separator is a colon (:)<br>ex) 02:03:04:05<br>-Interest-free installments for 2,3,4,5 months |

<br>

## Interest-free installment information API

### Over-view
Interest-free installment information API can check interest-free about card companies and amount range.

<br>

### Example code

```bash
curl -X GET 'https://api.nicepay.co.kr/v1/card/interest-free?useAuth=true&ediDate={ISO 8601 format date}' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic YWYwZDExNjIzNmRmNDM3ZjgzMT...'
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

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:-----:|:------:|:--------|
| useAuth       |  Boolean   |   O   |   -   | true : checkout or payment window <br> false : billing or key-in  |
| ediDate  | String    |   | -         | Full Text Creation Date<br>ISO 8601 Format |
| mid           |  String   |   　   |  10   | [Optional] Merchant ID separately contracted with Nice Payments |
| signData      | String    |       | 256       | Forgery Verification Data<br>Generation rule: hex(sha256(ediDate + SecretKey) |
| returnCharSet | String    |       | 10        | utf-8(Default) / euc-kr |
<br>

### Interest-free installment information Response parameter

```bash
Content-type: application/json
```

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| ediDate  | String    |   | -         | Full Text Creation Date<br>ISO 8601 Format |
| signature  | String  |   　   |  256   | Forgery Verification Data<br>Generation rule: hex(sha256(ediDate + SecretKey)  |


#### Interest-free installment information <img src="https://img.shields.io/badge/-Array-blueviolet">

| Parameter    |                 |              |   Type   |  Required   |  Bytes  | Description  |
|:-------------|:----------------|:-------------|:--------:|:-----:|:------:|:-------------------|
| interestFree |                 |              |  Array   |       |   -    | All interest-free installment information<br>- All interest-free information provided by NICEPAY and interest-free information applied to the store will be answered.<br>- The interest-free information object for each card code is answered.  |
|              | cardCode        |              |  String  |   O   |   3    | Card company code   |
|              | cardName        |              |  String  |   O   |   20   | Card issuer name <br> ex) BC  |
|              | freeInstallment |              |  Array   |   O   |   -    | Interest-free installment information |
|              |                 | minAmt       |   Int    |   O   |   12   | Minimum amount for event<br>ex) 50000                                                                       |
|              |                 | maxAmt       |   Int    |   O   |   14   | Maximum amount for event<br>ex) 99999999999999                                                              |
|              |                 | installment  |  String  |   O   |  100   | Interest-free installment months<br>Separator is a colon (:)<br>ex) 02:03:04:05<br>-Interest-free installments for 2,3,4,5 months   |
|              |                 | eventToDate  |  String  |   O   |   -    | Event deadline<br>ISO 8601 format                                                                        |

<br>