## Webhook

You can use Webhook to implement additional business logic by receiving API events as server-side responses.

- If you use a payment method such as virtual account that causes a time difference between virtual account creation and deposit time, webhook implementation is absolutely necessary.

> **⚠️ Important:** Webhook registration/inquiry/delete/update is not available in [Sandbox](../info/nicepay-info-sandbox.md); test against Live once your integration is ready.  
> When you register or update a webhook URL, NicePay first sends a test request to that URL. The request fails with [`U336`](../code/nicepay-code.md#api-response-code) if NicePay cannot reach the URL, `U337` if the response status is not `200`, and `U338` if the response body is not `OK`.  
> To test payment and cancellation events, make a small Live payment with a payment method that has a registered webhook URL, then cancel it. NicePay sends a webhook for the payment and another for the cancellation.  

<br>

### Over-view
<img alt="Sequence diagram of the webhook event delivery flow: NicePay pushes a payment or status-change event to the merchant server's registered webhook endpoint, the merchant server checks the signature and amount and processes the event, then responds with HTTP 200 and an OK body to acknowledge receipt; if delivery fails (network error, non-200 response, or a body other than OK), NicePay retries delivery on a configured schedule until it receives an OK acknowledgement" src="../image/payment-webhook.svg" width="800px">

<br>

### Webhook dispatch flow
- When an event occurs, data is delivered to the registered webhook `endpoint`.  
- Process business logic after checking the delivered webhook data
- After processing business logic, print an `OK` string (case-insensitive, e.g. `OK` or `ok` both work) in `HTTP Response body` and respond with `HTTP Status 200`.
- If delivery fails (network error, a non-`200` response, or a response body other than `OK`), NicePay automatically retries on a schedule configured for your account; [open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) if you need the exact retry count/interval. Because the same event can be redelivered, process it idempotently (for example, key your handling on `tid`/`orderId` and skip events you've already applied). Once the retry limit is reached, NicePay emails your account's registered admin address and stops retrying that event.

<br>

The following code is an example of a response message with the "ok" status.   
If the response message does not contain the "ok" status (case-insensitive), the event delivery is treated as a failure.   

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

> **⚠️ Important:** If there is no `OK` string (case-insensitive) in the `HTTP Response body`, it is treated as a failure.  
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

### Create a webhook <img alt="Beta version" src="https://img.shields.io/badge/-Beta version-B60205">

```bash
POST /v1/webhook
HTTP/1.1    
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

### Create webhook Request Parameter

Required: Yes = always send; No = optional; Conditional = send in the case stated in Description. Bytes = maximum length in bytes.

| Parameter | Type | Required | Bytes | Description |
|:--------------|:-----:|:-----:|:-----:|:----------|
|    `method`     | String  |  Yes  | 20	  |  all : all payment methods <br> card : local cards <br> bank : bank transfer <br> vbank : virtual account  <br> cellphone : carrier billing | 
| `url` | String | Yes | 200 | The URL of the webhook endpoint |
| `managerEmail` | String | No | 255 |Stored and echoed back on lookup, but NOT the address NicePay emails on delivery failure; that notification goes to your merchant account's registered admin email instead|

<br>

### Create webhook Response Parameter

Required: Yes = has a non-empty value in every response whose `resultCode` is `0000`; No = can be `null`, empty, or left out. For a field of an object or array, Yes applies whenever that object or array element is present.

| Parameter | Field | Type   |  Required   |  Bytes  | Description  |
|:----------|:----:|:------:|:-----------:|:-------:|:-------------|
| `resultCode`|      | String | Yes         | 4       | 0000 : success / other failure |
| `resultMsg` |      | String | Yes         | 100     | Result message |
| `urls` | | Array | No | | All webhook URLs registered for your account after this request, one element per payment method, in no fixed order |
|           | `method` | String | Yes | 20 | Payment method of this URL<br>card : local cards <br> bank : bank transfer <br> vbank : virtual account <br> cellphone : carrier billing<br>A URL registered with `all` is returned as four elements, one per method |
|           | `url` | String | Yes | 200 | The URL of the webhook endpoint |
|           | `managerEmail` | String | No | 255 | Stored and echoed back on lookup, but NOT the address NicePay emails on delivery failure; that notification goes to your merchant account's registered admin email instead<br>`null` if you did not send one |
| `messageSource` | | String | Yes | | Always `nicepay` for this API |

Related error codes: `U100`, `U111`, `U133`, `U333`, `U334`, `U335`, `U336`, `U337`, `U338`, `U700`, `U701`, see [API Response code](../code/nicepay-code.md#api-response-code).

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
    "messageSource": "nicepay",
    "urls": [
        {
            "method": "card",
            "url": "https://your-webhook.url",
            "managerEmail": null
        }
    ]
}
```

### Retrieve a webhook <img alt="Beta version" src="https://img.shields.io/badge/-Beta version-B60205">

```bash
GET /v1/webhook
HTTP/1.1    
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

### Retrieve webhook Request Parameter

This endpoint takes no request parameters beyond the `Authorization` header.

<br>

### Retrieve webhook Response Parameter

| Parameter | Field | Type   |  Required   |  Bytes  | Description  |
|:----------|:----:|:------:|:-----------:|:-------:|:-------------|
| `resultCode`|      | String | Yes         | 4       | 0000 : success / other failure |
| `resultMsg` |      | String | Yes         | 100     | Result message |
| `urls` | | Array | Yes | | All webhook URLs registered for your account, one element per payment method, in no fixed order<br>If none is registered, `resultCode` is [`U111`](../code/nicepay-code.md#api-response-code) and `urls` is an empty array |
|           | `method` | String | Yes | 20 | Payment method of this URL<br>card : local cards <br> bank : bank transfer <br> vbank : virtual account <br> cellphone : carrier billing<br>A URL registered with `all` is returned as four elements, one per method |
|           | `url` | String | Yes | 200 | The URL of the webhook endpoint |
|           | `managerEmail` | String | No | 255 | Stored and echoed back on lookup, but NOT the address NicePay emails on delivery failure; that notification goes to your merchant account's registered admin email instead<br>`null` if you did not send one |
| `messageSource` | | String | Yes | | Always `nicepay` for this API |

Related error codes: `U100`, `U111`, `U133`, `U333`, `U334`, `U335`, `U336`, `U337`, `U338`, `U700`, `U701`, see [API Response code](../code/nicepay-code.md#api-response-code).


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

### Delete a webhook <img alt="Beta version" src="https://img.shields.io/badge/-Beta version-B60205">

```bash
POST /v1/webhook/{method}/delete
HTTP/1.1    
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

### Delete webhook Request Parameter

| Parameter | Type | Required | Bytes | Description |
|:--------------|:-----:|:-----:|:-----:|:----------|
|    `method`     | String  |  Yes  | 20	  |  all : all payment methods <br> card : local cards <br> bank : bank transfer <br> vbank : virtual account  <br> cellphone : carrier billing | 

<br>

### Delete webhook Response Parameter

| Parameter | Field | Type   |  Required   |  Bytes  | Description  |
|:----------|:----:|:------:|:-----------:|:-------:|:-------------|
| `resultCode`|      | String | Yes         | 4       | 0000 : success / other failure |
| `resultMsg` |      | String | Yes         | 100     | Result message |
| `urls` | | Array | No | | All webhook URLs registered for your account after this request, one element per payment method, in no fixed order<br>An empty array after you delete the last URL |
|           | `method` | String | Yes | 20 | Payment method of this URL<br>card : local cards <br> bank : bank transfer <br> vbank : virtual account <br> cellphone : carrier billing<br>A URL registered with `all` is returned as four elements, one per method |
|           | `url` | String | Yes | 200 | The URL of the webhook endpoint |
|           | `managerEmail` | String | No | 255 | Stored and echoed back on lookup, but NOT the address NicePay emails on delivery failure; that notification goes to your merchant account's registered admin email instead<br>`null` if you did not send one |
| `messageSource` | | String | Yes | | Always `nicepay` for this API |

Related error codes: `U100`, `U111`, `U133`, `U333`, `U334`, `U335`, `U336`, `U337`, `U338`, `U700`, `U701`, see [API Response code](../code/nicepay-code.md#api-response-code).

<br><br>

### Update a webhook example code
```bash
curl --location --request POST 'https://api.nicepay.co.kr/v1/webhook/{method}/update' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UjFfOTRlYjNhNGEzMDI2NGZkYmE4MmNlMGQwNWI0NjUwMTI6MTJjZGUxMjQ0OWM2NDQ5N2E4NjEwNDc1OWI4MzA2YjY=' \
--data '{"url":"https://your-new-webhook.url"}'
```

```bash
{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다."
    ...
}
```

### Update a webhook <img alt="Beta version" src="https://img.shields.io/badge/-Beta version-B60205">

```bash
POST /v1/webhook/{method}/update
HTTP/1.1    
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

### Update webhook Request Parameter

| Parameter | Type | Required | Bytes | Description |
|:--------------|:-----:|:-----:|:-----:|:----------|
|    `method`     | String  |  Yes  | 20	  |  all : all payment methods <br> card : local cards <br> bank : bank transfer <br> vbank : virtual account  <br> cellphone : carrier billing | 
| `url` | String | Yes | 200 | The URL of the webhook endpoint |
| `managerEmail` | String | No | 255 |Stored and echoed back on lookup, but NOT the address NicePay emails on delivery failure; that notification goes to your merchant account's registered admin email instead|

<br>

### Update webhook Response Parameter

| Parameter | Field | Type   |  Required   |  Bytes  | Description  |
|:----------|:----:|:------:|:-----------:|:-------:|:-------------|
| `resultCode`|      | String | Yes         | 4       | 0000 : success / other failure |
| `resultMsg` |      | String | Yes         | 100     | Result message |
| `urls` | | Array | No | | All webhook URLs registered for your account after this request, one element per payment method, in no fixed order |
|           | `method` | String | Yes | 20 | Payment method of this URL<br>card : local cards <br> bank : bank transfer <br> vbank : virtual account <br> cellphone : carrier billing<br>A URL registered with `all` is returned as four elements, one per method |
|           | `url` | String | Yes | 200 | The URL of the webhook endpoint |
|           | `managerEmail` | String | No | 255 | Stored and echoed back on lookup, but NOT the address NicePay emails on delivery failure; that notification goes to your merchant account's registered admin email instead<br>`null` if you did not send one |
| `messageSource` | | String | Yes | | Always `nicepay` for this API |

Related error codes: `U100`, `U111`, `U133`, `U333`, `U334`, `U335`, `U336`, `U337`, `U338`, `U700`, `U701`, see [API Response code](../code/nicepay-code.md#api-response-code).

<br><br>

### Webhook Response parameter

```bash
POST
Content-type: application/json;charset=utf-8
```

A webhook for a Key-in payment has the fields of [Key-in Payment Response Parameter](./nicepay-api-keyin.md#key-in-payment-response-parameter) instead of the table below.

| Parameter | Type | Required | Bytes | Description |
|:------------------|:--------:|:-----:|:------:|:---------------------------------------------------------------------------------------------------------------------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `sessionId` | String | No | 256 | Checkout session ID of the payment<br>Sent only when the payment was made through Checkout; otherwise the key is left out. A webhook for a cancellation made with `tid` does not include it |
| `tid` | String | Yes | 30 | NICEPAY transaction ID |
| `cancelledTid` | String | No | 30 | Cancellation transaction ID<br>- Responded only with cancellation requests<br>- Use when finding canceled transaction information in the cancels object. |
| `orderId` | String | Yes | 64 | Unique order number |
| `ediDate` | String | Yes | - | Response message creation date and time (ISO 8601 format) |
| `signature` | String | No | 256 | Forgery verification data<br>- Respond only to valid transactions<br>- Creation rule: hex(sha256(tid + amount + ediDate+ SecretKey))<br>- For data validation, it is recommended to implement a comparison at business logic |
| `status` | String | Yes | 20 | Payment processing status<br>paid: payment completed<br> ready: ready<br>failed: payment failed<br>cancelled: cancelled<br>partialCancelled: partially cancelled<br>['paid', 'ready', 'failed', 'cancelled', 'partialCancelled'] |
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
| `approveNo` | String | No | 30 | Authorization Number<br>Credit Card, Bank Transfer, Mobile Phone |
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
| `bank`       |          |  Object  | No |       | bank object    |
|           | `bankCode`  |  String  | Yes |   3    | bank code  |
|            | `bankName`  |  String  | Yes |   20   | bank name (euc-kr encoded) |

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
| | `receiptUrl` | String | Yes | 200 | <br>Receipt URL for user |
| | `couponAmt` | Int | No | 12 | Cancellation amount of coupon <br> *Optional|