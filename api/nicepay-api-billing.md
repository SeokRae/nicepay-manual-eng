## Recurring Payment

<br>

## Recurring Payment - Create Token

You can implement subscribtion payments through the Recurring Payment API.  
If you pass the card information through the `/v1/subscribe/regist` API, you can receive an encrypted Token(bid) in response.  
After that, if you pass the encrypted Token(bid) through the `/v1/subscribe/{bid}/payments` API with payment amount, Authorization will be occurred with the registered card.  

> #### ⚠️ Important  
> Multiple Token can be generated with one card and All issued bid can be used until deleted.  
> This describes NicePay's own token limit only, see the `F201` note under [Create Token(bid) Response Parameter](#create-tokenbid-response-parameter) for a case where a `regist` call can still fail on an already-registered card.  

<br>

**Before you start**, you'll need:
- A [Client and Secret key](../info/nicepay-info-key.md) issued from the NicePay admin console
- An `Authorization` header built from those keys, see [Basic and Bearer authentication](../info/nicepay-info-basic-token.md)
- We recommend testing against the [Sandbox](../info/nicepay-info-sandbox.md) first, then switching to Live once verified

<br>

### Create Token Over-view
<img alt="Recurring payment token registration flow diagram: the customer inputs card information, the merchant server requests a token (bid) from NicePay, and the token is returned for use in repetitive approval requests without re-entering card details" src="../image/payment-subscribe.svg" width="800px"> 

### Example code

**Default (AES-128, no `encMode`)**

```bash
curl -X POST 'https://api.nicepay.co.kr/v1/subscribe/regist' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic ZWVjOGQzNTA4Y2IwNDI1ZGI5NTViMzBiZjM5...' 
--data '{
    "encData": "2127975b6d82c36136ba8197a997a994f6c086ff75a6d35e514c54a1e686545e60b76f11bec706de1082e43dd74ae5c5f0709dc1eca6c3cd20e1c0e9e9b7a85c6505461c91c865d82072e41ba5284bd7",
    "orderId": "merchant-order-id"
}'
```

> `encData` here is encrypted with AES-128 (see [encData Field Encryption Example (AES-128)](#encdata-field-encryption-example-aes-128) below). No `encMode` field is sent, so the default algorithm applies.

**AES-256 (`encMode=A2`)**

```bash
curl -X POST 'https://api.nicepay.co.kr/v1/subscribe/regist' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic ZWVjOGQzNTA4Y2IwNDI1ZGI5NTViMzBiZjM5...' 
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

| Parameter     | Type      | required | bytes | Description |
|:--------------|:--------:|:-----:|:------:|:---------------|
| encData       |  String  |   O   |  512   | Payment Information Encryption Data<br>- Encryption Algorithm: AES128<br>- Encryption Details: AES/CBC/PKCS5padding<br>- Encoding Encryption Result: Hex Encoding<br>- Encryption KEY: 16 digits before SecretKey<br>- IV : 16 digits before SecretKey<br><br> Hex(AES(cardNo=value&expYear=YY&expMonth=MM&idNo=value&cardPw=value)) |
| orderId       |  String  |   O   |   64   | Unique order number or payment number managed by the merchant<br>-In case of partial cancellation, orderId cannot be reusable |
| buyerName     |  String  |   　   |   30   | Buyer name  |
| buyerEmail    |  String  |   　   |   60   | Buyer Email |
| buyerTel      |  String  |   　   |   20   | Buyer phone number<br> *Number only|
| encMode       |  String  |   　   |   10   | Encryption Mode<br>`encData` Field Encryption Algorithm Definition<br><br> A2 : AES256<br>Encryption Algorithm : AES256<br> Encryption Detail : AES/CBC/PKCS5padding <br> Encryption Result Encoding : Hex Encoding <br> *Encryption KEY: SecretKey (32byte)<br>•IV: 16 digits before the SecretKey |
| ediDate       |  String  |   　   |   -    | Response message creation date and time (ISO 8601 format) |
| signData      |   String    |   　   |  256   | Forgery Verification Data<br> Rule : hex(sha256(orderId + ediDate +   SecretKey)) |
| returnCharSet | String    |       | 10        | utf-8(Default) / euc-kr |

<br>

### encData Field Details

| Parameter     | Type      | required | bytes | Description |
|:--------------|:--------:|:-----:|:------:|:---------------|
| cardNo     |  String  |     O      |   16   | Card Number<br>Numbers only     |
| expYear    |  String  |     O      |   2    | expiration year<br>format : YY  |
| expMonth   |  String  |     O      |   2    | expiration month<br>format : MM  |
| idNo       |  String  |  Optional  |   13   | Individual(Date of birth, 6 digits) : YYMMDD <br/> Corporation: business number of korea, 10 digits  |
| cardPw     |  String  |  Optional  |   2    | First 2 digits of the card password |

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

| Parameter     | Type      | required | bytes | Description |
|:--------------|:---------:|:--------:|:-----:|:------------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg  | String | O | 100 | Result message |
| tid        | String | O |  30   | Transaction ID<br>Ex) nictest00m01011104191651325596  |
| orderId    | String | O | 64        | Your Unique order ID *Not reusable |
| bid        | String |   |  30   | Token<br>- Key value linked to card information, delivered when calling Token Authorization API<br>Ex) BIKYnictest00m1104191651325596  |
| authDate   | String |   |   -   | Date created<br>ISO 8601 format   |
| cardCode   | String |   |   3   | Card company code |
| cardName   | String |   |  20   | Card issuer name <br> ex) BC   |
| messageSource | String | |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|
| status     | String | O |   6   | issued: bid was created successfully<br>failed: `regist` call failed, see `resultCode` |

> #### ⚠️ Important  
> Even though NicePay allows multiple tokens per card, a `regist` call can still fail with [`F201`](../code/nicepay-code.md#api-response-code) ("card already registered", bill key issuance failed), returned as-is in `resultCode` with `status: failed`, no `bid`, and `messageSource: external`. That check happens on the card issuer/payment network side, not NicePay's, so the exact conditions that trigger it are not documented here; contact NicePay support if you hit it unexpectedly.  
> `F201` is specific to card-based bill-key issuance (this API, and Checkout's `cardBill` method, see [Hosted Payment Page Request Parameter](./nicepay-api-payment-window-url.md#hosted-payment-page-request-parameter-)); Recurring Payment enrolled through Naver Pay/Kakao Pay/Toss Pay checkout (`naverCardBill`/`naverPointBill`/`kakaoBill`/`tosspayBill`) goes through a separate corePG code family and is not affected by it.  

<br>


## Recurring Payment - Authorization
- Token(bid) authorization means payment (approval) processing through the issued Token(bid).
- If you call the `/v1/subscribe/{bid}/payments` API through the registered Token(bid), payment (approval) will be processed.
- For Token(bid) approval, the `Create Token`process is required.

<br>

### Authorization Over-view
<img alt="Token authorization flow diagram: the merchant server requests authorization using the customer's previously registered token (bid) and receives the approval response, without the customer re-entering card details" src="../image/payment-subscribe-authorization.svg" width="800px">  

### Token authorization example
```bash
curl -X POST 'https://api.nicepay.co.kr/v1/subscribe/BIKYnicuntct2m2107272028532670/payments' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic ZWVjOGQzNTA4Y2IwNDI1ZGI5NTViMzBi...' 
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

| Parameter     | Type      | required | bytes | Description |
|:--------------|:---------:|:--------:|:------:|:-----------|
| orderId         |  String  |   O   |   64   | *Not reusable |
| amount          |   Int    |   O   |   12   | Payment amount  |
| goodsName       |  String  |   O   |   40   | Product name  |
| cardQuota       |   Int    |   O   |   2    | Installment Month<br>0: Pay in full amount, 2:2 months, 3:3 months … |
| useShopInterest | Boolean  |   O   |   -    | The store pays the installment interest of the customer<br>(currently, only false is available) |
| buyerName       |  String  |   　   |   30   | Buyer name |
| buyerTel        |  String  |   　   |   20   | Buyer phone number<br>*Number only   |
| buyerEmail      |  String  |   　   |   60   | Buyer Email |
| taxFreeAmt      |   Int    |   　   |   12   | Tax-free amount  |
| mallReserved    |  String  |   　   |  500   | Spare field for store information delivery<br>It is recommended to use JSON string format.<br>However, double quotation marks (") cannot be used  |
| ediDate         |  String  |   　   |   -    | Response message creation date and time <br>ISO 8601 format |
| signData        |  String  |   　   |  256   | Forgery Verification Data<br> Rule : hex(sha256(orderId + bid + ediDate + SecretKey))      |
| returnCharSet | String    |       | 10        | utf-8(Default) / euc-kr |

<br>

### Recurring Payment - Authorization Response Parameter

```bash
POST
Content-type: application/json
```

| Parameter | Type | required | Bytes | Description |
|:----------|:----:|:--------:|:------:|:-----------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| tid | String | O | 30 | NICEPAY transaction ID |
| cancelledTid | String | | 30 | Cancellation transaction ID<br>- Responded only with cancellation requests<br>- Use when finding canceled transaction information in the cancels object. |
| orderId | String | O | 64 | Unique order number |
| ediDate | String | O | - | Response message creation date and time (ISO 8601 format) |
| signature | String | | 256 | Forgery verification data<br>- Respond only to valid transactions<br>- Creation rule: hex(sha256(tid + amount + ediDate+ SecretKey))<br>- For data validation, it is recommended to implement a comparison at business logic |
| status | String | O | 20 | Payment processing status<br>paid: payment completed<br> ready: ready<br>failed: payment failed<br>cancelled: cancelled<br>partialCancelled: partially cancelled<br>['paid', 'ready', 'failed', 'cancelled', 'partialCancelled'] |
| paidAt | String | O | - | Time of payment completed ISO 8601 format<br>If payment is not completed, return 0 |
| failedAt | String | O | - | Time of payment failure ISO 8601 format<br>If not payment is not failed, return 0 |
| cancelledAt | String | O | - | Payment cancellation time ISO 8601 format<br>If it is not cancellation request, return 0<br>In case of partial cancellation, the last cancellation time will be return |
| payMethod | String | O | 10 | Payment method<br><br>card: credit card, <br>vbank: virtual account, <br>bank: account transfer, <br>cellphone: mobile phone, <br>naverpay=Naver Pay, <br>kakaopay=Kakao Pay, <br>samsungpay=Samsung Pay, <br>tosspay=Toss Pay |
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
| | canPartCancel | Boolean | O | - | Whether partial cancellation is possible<br>true: Possible, false: Impossible |
| | acquCardCode | String | O | 3 | Acquirer code |
| | acquCardName | String | O | 100 | Acquirer Name |

<br>

### After a Recurring Payment charge

- A charge made with `/v1/subscribe/{bid}/payments` does not have its own cancel API. Cancel or refund it with the standard [Cancel request with tid](./nicepay-api-cancel.md#cancel-request-parameter-with-tid), using the `tid` from the Authorization Response above.
- You can look up a charge anytime via [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id) with the `tid`.
- A token (`bid`) is not deleted automatically and stays usable until you remove it — see [Delete Token](#delete-token) below.
- Related error codes: `U309`, `A126`, `A253`, `A255`, `U113`, `F110`, `F115`, `F116`, see [API Response code](../code/nicepay-code.md#api-response-code).

<br>


## Delete Token

`Delete Token` refers to the process of deleting the issued Token(bid).  
If you pass the registered billkey to the `/v1/subscribe/{bid}/expire` API, the Token(bid) will be deleted.  
Deleted Token(bid) cannot be restored or approved. 

<br>

### Delete Token Over-view
<img alt="Token deletion flow diagram: the merchant server requests deletion of the customer's registered token (bid) from NicePay and receives confirmation that card registration was cancelled" src="../image/payment-subscribe-delete.svg" width="800px">  

### Delete Token(bid) Example code

``` bash
curl -X POST 'https://api.nicepay.co.kr/v1/subscribe/BIKYnicuntct2m2107272028532670/expire' 
-H 'Content-Type: application/json' 
-H 'Authorization: Basic ZWVjOGQzNTA4Y2IwNDI1ZGI5NTViMzBiZjM...' 
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

| Parameter     | Type   | required | Bytes | Description |
|:--------------|:------:|:--------:|:------:|:-----------|
| orderId       | String |  O       |  64   | Your unique order ID *Not reusable |
| ediDate       | String |          |   -   | Creation Date<br> ISO 8601 format |
| signData      | String |          |  256  | Forgery Verification Data<br>Rule : hex(sha256(orderId + bid +   ediDate + SecretKey))|
| returnCharSet | String |          |  10   | utf-8(Default) / euc-kr |	

<br>

### Delete Token(bid) Response Parameter

```bash
POST
Content-type: application/json
```

| Parameter | Type | required | Bytes | Description |
|:----------|:----:|:--------:|:------:|:-----------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| tid | String | O | 30 | NICEPAY transaction ID |
| orderId | String | O | 64 | Your Unique order ID |
| bid        | String | O    | 30   | Token |
| authDate   | String | 　   | -    | ISO 8601 format<br>*Returned if processing is successful. |
| messageSource | String | |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|
