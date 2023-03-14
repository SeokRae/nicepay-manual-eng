## Sandbox

Sandbox and Live have a difference in domain and Client ID.   
Please use it after checking whether the issued Client ID is for Sandbox or Live.   
Sandbox responds with TEST data, and no actual approval occurs.  

### Advantages of sandbox

- Immediate test and development are possible.  
- It is possible to test comfortably because actual payment does not occur.  
- A test system that does not affect the live environment.  

### Using Sandbox and Live domains

| Service              | Domain                    | IP Address                      | Direction       |
|--------------------|---------------------------|------------------------------------|----------|
| Create Hosted Payment (live)   | pay.nicepay.co.kr/v1/checkout | 121.133.126.85/27                  | OUTBOUND |
| Create Hosted Payment (sandbox)  | stg-pay.nicepay.co.kr/v1/checkout | - | OUTBOUND |
| API (live)  | api.nicepay.co.kr         | 121.133.126.83/27                  | OUTBOUND |
| API (sandbox) | sandbox-api.nicepay.co.kr | 121.133.126.84/27                  | OUTBOUND |
| Webhook  | -  | 121.133.126.86 <br> 121.133.126.87 | INBOUND  |

```bash
// Test key
// stg-pay.nicepay.co.kr/v1/checkout

Client : S1_ce1bb1ebebc44fe1a3f7cec976c83ea7		
Secret : 13e969a77a0545799242ccc3915243d3
```

```bash
// Test key
// pay.nicepay.co.kr/v1/checkout

Client : R1_94eb3a4a30264fdba82ce0d05b465012
Secret : 12cde12449c64497a86104759b8306b6
Authorization : Basic UjFfOTRlYjNhNGEzMDI2NGZkYmE4MmNlMGQwNWI0NjUwMTI6MTJjZGUxMjQ0OWM2NDQ5N2E4NjEwNDc1OWI4MzA2YjY=
```

```bash
// Test key
// api.nicepay.co.kr

Client : R1_94eb3a4a30264fdba82ce0d05b465012
Secret : 12cde12449c64497a86104759b8306b6
Authorization : Basic UjFfOTRlYjNhNGEzMDI2NGZkYmE4MmNlMGQwNWI0NjUwMTI6MTJjZGUxMjQ0OWM2NDQ5N2E4NjEwNDc1OWI4MzA2YjY=
```

```bash
// Test key
// pay.nicepay.co.kr/v1/access-token

Client : R1_77988f331a1a411b8e215d07e2dddf5c
Secret : be0d7e00bf434ba1bbec7f827662ab30
Authorization : Basic UjFfNzc5ODhmMzMxYTFhNDExYjhlMjE1ZDA3ZTJkZGRmNWM6YmUwZDdlMDBiZjQzNGJhMWJiZWM3ZjgyNzY2MmFiMzA=
```

```bash
// Test key
// sandbox-api.nicepay.co.kr

Client : S1_ce1bb1ebebc44fe1a3f7cec976c83ea7		
Secret : 13e969a77a0545799242ccc3915243d3
Authorization : Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM= 
```

<br><br>

## Using Sandbox API's

<img src="../image/payment-url.svg" width="800px">

This is an explanation of how to generate a checkout sessionId and call a checkout page through the Sandbox.

Call the Checkout API to generate a sessionId.
- Call the Checkout API with the necessary parameters.  
- Receive the generated sessionId in the response.  
  
Use the `url` for Checkout.
- The Checkout URL will be in the following format:
- https://{sandbox-domain}/v1/checkout/{sessionId}

Call the Checkout URL.
- In the Sandbox, card company authentication is skipped and the approval response is sent to the returnUrl.


Please refer to the link for more detailed information.  
[Payment Request (Hosted Payment Page)](/api/nicepay-api-payment-window-url.md) 

<br>

### Test key  
Client : S1_ce1bb1ebebc44fe1a3f7cec976c83ea7		
Secret : 13e969a77a0545799242ccc3915243d3  
Authorization : Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=   

<br>

## Create a checkout
```bash
curl --location 'https://stg-pay.nicepay.co.kr/v1/checkout' \
--header 'Content-Type: application/json' \
--data '{
  "clientId": "S1_ce1bb1ebebc44fe1a3f7cec976c83ea7",
  "method": "card",
  "orderId": "64101a9c197f7",
  "goodsName" : "test",
  "amount": 1004,
  "returnUrl": "http://your-return-url.com"
}'
```

<br>

## Return a checkout
```bash
Content-type: application/json;charset=utf-8

{
    "sessionId": "NICEORD_TRK2_nicuntsx1m_2303141556293759",
    "url": "https://stg-pay.nicepay.co.kr/v1/checkout/NICEORD_TRK2_nicuntsx1m_2303141556293759",
    "resultCode": "0000",
    "resultMsg": "success"
}
```

<br>

## Request a checkout page
```bash
The client follows the link to the checkout page.

https://stg-pay.nicepay.co.kr/v1/checkout/NICEORD_TRK1_nicuntsx1m_2303141453392115
```

<img src="../image/sandbox-checkout.png" width="800px">

- This is a dummy page without actual card company authentication.

<br>

## Return a checkout
```bash
Content-type: application/json;charset=utf-8  

The response data will be sent to the returnUrl set in the checkout request.

{
  "success": "true",
  "authToken": "NICEUNTTC97DE0D4CF9FD01F4459502B20E61D6D",
  "tid": "UT0000104m01012303141557092002",
  "orderId": "64101a9c197f7",
  "clientId": "S1_ce1bb1ebebc44fe1a3f7cec976c83ea7",
  "mallReserved": "",
  "resultCode": "0000",
  "resultMsg": "정상 처리되었습니다.",
  "amount": 1004,
  "goodsName": "test",
  "channel": "pc",
  "status": "paid",
  "ediDate": "2023-03-14T15:57:09.169+0900",
  "signature": "171dbcb700698881c1b2944cc87c8df9109790b58077bfbaa9123f289df95108",
  "paidAt": "2023-03-14T15:57:09.000+0900",
  "failedAt": "0",
  "payMethod": "card",
  "useEscrow": false,
  "currency": "KRW",
  "approveNo": "000000",
  "couponAmt": "",
  "buyerName": "",
  "buyerTel": "",
  "buyerEmail": "",
  "issuedCashReceipt": false,
  "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m01012303141557092002",
  "mallUserId": "",
  "cardCode": "04",
  "cardName": "삼성",
  "cardQuota": "0",
  "isInterestFree": "false",
  "cardType": "credit",
  "canPartCancel": "true",
  "acquCardCode": "04",
  "acquCardName": "삼성"
}
```

Through the page below, you can easily check the flow when conducting a test  
http://35.228.103.98/untact-sandbox/checkout-sandbox.php  

<br><br>

## Transaction Status Inquiry

You can check `Transaction Status` through the response tid(transaction ID) received in the Sandbox.  


Please refer to the link for more detailed information.  
[Transaction Status Inquiry-Transaction status](/api/nicepay-api-retrieve.md#retrieve-a-transaction-with-tidtransaction-id)

<br>

### Transaction Status Inquiry with tid

<br>

```bash
GET /v1/payments/{tid} 
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```
<br>

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/payments/UT0000104m01012303141557092002' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM='
```

<br>

```bash
Response

Content-type: application/json;charset=utf-8 


{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다.",
    "tid": "UT0000104m01012303141557092002",
    "cancelledTid": null,
    "orderId": "64101a9c197f7",
    "ediDate": "2023-03-14T16:28:08.521+0900",
    "signature": "cc446c6db155e571f0f2cda8a495876e1016615a759375230e7a53a0cd52a0c4",
    "status": "paid",
    "paidAt": "2023-03-14T15:57:09.000+0900",
    "failedAt": "0",
    "cancelledAt": "0",
    "payMethod": "card",
    "amount": 1004,
    "balanceAmt": 1004,
    "goodsName": "test",
    "mallReserved": null,
    "useEscrow": false,
    "currency": "KRW",
    "channel": "pc",
    "approveNo": "000000",
    "buyerName": null,
    "buyerTel": null,
    "buyerEmail": null,
    "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m01012303141557092002",
    "mallUserId": null,
    "issuedCashReceipt": false,
    "coupon": null,
    "card": {
        "cardCode": "04",
        "cardName": "삼성",
        "cardNum": "12341234****1234",
        "cardQuota": 0,
        "isInterestFree": false,
        "cardType": "credit",
        "canPartCancel": true,
        "acquCardCode": "04",
        "acquCardName": "삼성"
    },
    "vbank": null,
    "bank": null,
    "cellphone": null,
    "cancels": null,
    "cashReceipts": null
}
```

If it is difficult to check the TID, you can check it through the orderId.

Please refer to the link for more detailed information.  
[Transaction Status Inquiry-orderId](/api/nicepay-api-retrieve.md#retrieve-a-transaction-with-orderid)

<br>

### Transaction Status Inquiry with orderId

<br>

```bash
GET /v1/payments/find/{orderId}  
HTTP/1.1    
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

<br>

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/payments/find/64101a9c197f7' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM='
```

<br>

```bash
Response

Content-type: application/json;charset=utf-8 

{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다.",
    "tid": "UT0000104m01012303141557092002",
    "cancelledTid": null,
    "orderId": "64101a9c197f7",
    "ediDate": "2023-03-14T16:32:49.063+0900",
    "signature": "649c7f1636c96d3e6cf019bcf36458ec0eaface9ce2662bb89680bec218a54a7",
    "status": "paid",
    "paidAt": "2023-03-14T15:57:09.000+0900",
    "failedAt": "0",
    "cancelledAt": "0",
    "payMethod": "card",
    "amount": 1004,
    "balanceAmt": 1004,
    "goodsName": "test",
    "mallReserved": null,
    "useEscrow": false,
    "currency": "KRW",
    "channel": "pc",
    "approveNo": "000000",
    "buyerName": null,
    "buyerTel": null,
    "buyerEmail": null,
    "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m01012303141557092002",
    "mallUserId": null,
    "issuedCashReceipt": false,
    "coupon": null,
    "card": {
        "cardCode": "04",
        "cardName": "삼성",
        "cardNum": "12341234****1234",
        "cardQuota": 0,
        "isInterestFree": false,
        "cardType": "credit",
        "canPartCancel": true,
        "acquCardCode": "04",
        "acquCardName": "삼성"
    },
    "vbank": null,
    "bank": null,
    "cellphone": null,
    "cancels": null,
    "cashReceipts": null
}

```

## Cancel

In the Sandbox, only full cancellation is possible.  
If there is no amount value, a full cancellation will be processed, and if a partial cancellation is to be made, you must pass the amount value.

Please refer to the link for more detailed information.  
[Cancel request](/api/nicepay-api-cancel.md) 

<br>

```bash
POST /v1/payments/{tid}/cancel  
HTTP/1.1  
Host: api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

<br>

### Cancel exmplae

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/payments/UT0000104m01012303141557092002/cancel' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
--data '{
    "reason" : "sample-code",
    "orderId" : "merchant-order-id"
}'
```

<br>

```bash
Response

Content-type: application/json;charset=utf-8 

{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다.",
    "tid": "UT0000104m01012303141557092002",
    "cancelledTid": "UT0000104m01012303141557092002",
    "orderId": "64101a9c197f7",
    "ediDate": "2023-03-14T16:37:24.776+0900",
    "signature": "26e3f763efc77a41a0bdad24b996b4ed7f1db2867e767d9472d064be300f758c",
    "status": "cancelled",
    "paidAt": "2023-03-14T15:57:09.000+0900",
    "failedAt": "0",
    "cancelledAt": "2023-03-14T16:37:24.000+0900",
    "payMethod": "card",
    "amount": 1004,
    "balanceAmt": 0,
    "goodsName": "test",
    "mallReserved": null,
    "useEscrow": false,
    "currency": "KRW",
    "channel": "pc",
    "approveNo": "000000",
    "buyerName": null,
    "buyerTel": null,
    "buyerEmail": null,
    "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m01012303141557092002",
    "mallUserId": null,
    "issuedCashReceipt": false,
    "coupon": null,
    "card": {
        "cardCode": "04",
        "cardName": "삼성",
        "cardNum": "12341234****1234",
        "cardQuota": 0,
        "isInterestFree": false,
        "cardType": "credit",
        "canPartCancel": true,
        "acquCardCode": "04",
        "acquCardName": "삼성"
    },
    "vbank": null,
    "bank": null,
    "cellphone": null,
    "cancels": [
        {
            "tid": "UT0000104m01012303141557092002",
            "amount": 1004,
            "cancelledAt": "2023-03-14T16:37:24.000+0900",
            "reason": "고객요청",
            "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m01012303141557092002",
            "couponAmt": 0
        }
    ],
    "cashReceipts": null
}
```