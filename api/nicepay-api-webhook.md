## Webhook

Webhook is a function that can implement additional business logic by receiving API events as server-side responses.

- If you use a payment method such as virtual account that causes a time difference between virtual account creation and deposit time, webhook implementation is absolutely necessary.

<br>

### Webhook dispatch flow
- When an event occurs, data is delivered to the registered webhook `End-point`.  
- Process business logic after checking the delivered webhook data
- After processing business logic, print “OK” string in `HTTP Response body` and respond with `HTTP Status 200`.

<br>

The following code is an example of a response message with the "ok" status.   
If the response message does not contain the "ok" status, the webhook registration will not be successful.   

```java
// java spring example
@RequestMapping(value = "/hook") 
public ResponseEntity<String> hook(@RequestBody HashMap<String, Object> hookMap) throws Exception {
  String resultCode = hookMap.get("resultCode").toString();

  if(resultCode.equalsIgnoreCase("0000")){
    return ResponseEntity.status(HttpStatus.OK).body("ok");
  }
        
return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).build();
}
```

<br>

```python
// python flask example
@app.route('/hook', methods=['POST'])
def hook():
    print(request.json)
    return make_response("ok", 200)
```
<br>

### Delivery of webhook
- When payment (approved) is made (all payment methods)
- When a virtual account is issued (numbered)
- When the payment amount is deposited into the virtual account
- When payment is canceled

<br>

> ⚠️ IMPORTANT  
> If there is no "OK" string in the `HTTP Response body`, it is treated as a failure, so be careful.  
> Check the firewall policy to allow webhook `Inbound IP`.  
> Be sure to check the `signature` value and amount before processing business logic through webhook.  

<br><br>

### Create a webhook example code
```bash
curl --location --request POST 'https://api.nicepay.co.kr/v1/webhook' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UjFfOTRlYjNhNGEzMDI2NGZkYmE4MmNlMGQwNWI0NjUwMTI6MTJjZGUxMjQ0OWM2NDQ5N2E4NjEwNDc1OWI4MzA2YjY=' \
--data-raw '{"method":"vbank","url":"https://your-webhook.url"}'
```

```bash
{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다."
    ...
}
```

### Create a webhook <img src="https://img.shields.io/badge/-Beta version-red">

```bash
POST /v1/webhook
HTTP/1.1    
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

### Create webhook Request Parameter

| Parameter | Type | Required | Bytes | Description |
|:--------------|:-----:|:-----:|:-----:|:----------|
|    method     | String  |  O  | 20	  |  card : local cards <br> bank : bank transfer <br> vbank : virtual account  <br> cellphone : carrier billing | 
| url | String | O | 200 | The URL of the webhook endpoint |
| managerEmail | String |  | 255 |If a webhook error occurs, an email will be automatically sent|
| returnCharSet | String  |     | 10	  | Return encoding <br>utf-8(Default) / euc-kr	 | 

<br>

### Create webhook Response Parameter

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| method | String |  | 100 | all: all <br> card : local cards <br> bank : bank transfer <br> vbank : virtual account  <br> cellphone : carrier billing |
| url | String |  | 100 | The URL of the webhook endpoint |
| managerEmail | String |  | 100 |  |
| returnCharSet | String |  | 100 |  |

<br><br>


### Retrieve a webhook example code
```bash
curl --location --request GET 'https://api.nicepay.co.kr/v1/webhook' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UjFfOTRlYjNhNGEzMDI2NGZkYmE4MmNlMGQwNWI0NjUwMTI6MTJjZGUxMjQ0OWM2NDQ5N2E4NjEwNDc1OWI4MzA2YjY=' \
```

```bash
{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다.",
    "urls": {
        "method": "card",
        "url": "https://your-webhook.url"
        ...
    }
}
```

### Retrieve a webhook <img src="https://img.shields.io/badge/-Beta version-red">

```bash
GET /v1/webhook
HTTP/1.1    
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

### Retrieve webhook Request Parameter

| Parameter | Type | Required | Bytes | Description |
|:--------------|:-----:|:-----:|:-----:|:----------|
| returnCharSet | String  |     | 10	  | Return encoding <br>utf-8(Default) / euc-kr	 | 

<br>

### Retrieve webhook Response Parameter

| Parameter |      | Type   |  Required   |  Bytes  | Description  |
|:----------|:----:|:------:|:-----------:|:-------:|:-------------|
| resultCode|      | String | O           | 4       | 0000 : success / other failure |
| resultMsg |      | String | O           | 100     | Result message |
| urls      |      |        |             |         |              |
|           | method | String  |  O  | 20	  | card : local cards <br> bank : bank transfer <br> vbank : virtual account  <br> cellphone : carrier billing |
|           | url | String | O | 200 | The URL of the webhook endpoint |
|           | managerEmail | String |  | 255 |If a webhook error occurs, an email will be automatically sent|
|           | returnCharSet | String  |     | 10	  | Return encoding <br>utf-8(Default) / euc-kr	 | 


<br><br>

### Delete a webhook example code
```bash
curl --location --request POST 'https://api.nicepay.co.kr/v1/webhook/{method}/delete' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UjFfOTRlYjNhNGEzMDI2NGZkYmE4MmNlMGQwNWI0NjUwMTI6MTJjZGUxMjQ0OWM2NDQ5N2E4NjEwNDc1OWI4MzA2YjY=' \
```

```bash
{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다."
    ...
}
```

### Delete a webhook <img src="https://img.shields.io/badge/-Beta version-red">

```bash
GET /v1/webhook/{method}/delete
HTTP/1.1    
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

### Delete webhook Request Parameter

| Parameter | Type | Required | Bytes | Description |
|:--------------|:-----:|:-----:|:-----:|:----------|
|    method     | String  |  O  | 20	  |  card : local cards <br> bank : bank transfer <br> vbank : virtual account  <br> cellphone : carrier billing | 
| returnCharSet | String  |     | 10	  | Return encoding <br>utf-8(Default) / euc-kr	 | 

<br>

### Delete webhook Response Parameter

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |

<br><br>

### Webhook Response parameter

```bash
POST
Content-type: application/json;charset=utf-8
```

| Parameter | Type | required | Bytes | Description |
|:------------------|:--------:|:-----:|:------:|:---------------------------------------------------------------------------------------------------------------------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
|   sessionId    | String  |  O  | 64	  | Merchant unique session id, issued by merchant | 
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