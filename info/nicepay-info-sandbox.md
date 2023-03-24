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
| API (live)  | api.nicepay.co.kr         | 121.133.126.83/27                  | OUTBOUND |
| API (sandbox) | sandbox-api.nicepay.co.kr | 121.133.126.84/27                  | OUTBOUND |
| Webhook  | -  | 121.133.126.86 <br> 121.133.126.87 | INBOUND  |

```bash
// Test key
// sandbox-api.nicepay.co.kr

Client : S1_ce1bb1ebebc44fe1a3f7cec976c83ea7		
Secret : 13e969a77a0545799242ccc3915243d3
Authorization : Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM= 
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

The session ID and order ID must be unique.

<br>

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/checkout' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
--data '{
    "sessionId" : "641d555b91ae1",
    "orderId" : "641d555b91ae6",    
    "method" : "all",    
    "clientId" : "S1_ce1bb1ebebc44fe1a3f7cec976c83ea7",
    "amount" : 1004,
    "goodsName" : "test",
    "returnUrl": "http://your-return-url.com",
    "language" : "EN",
    "fakeAuth": "true"
}'
```

<br>

## Return a checkout
```bash
Content-type: application/json;charset=utf-8

{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다.",
    "sessionId": "641d555b91ae1",
    "orderId": "641d555b91ae6",
    "clientId": "S1_ce1bb1ebebc44fe1a3f7cec976c83ea7",
    "tid": null,
    "amount": 1004,
    "goodsName": "test",
    "returnUrl": "http://your-return-url.com",
    "apprStatus": "ready",
    "skinType": null,
    "taxFreeAmt": null,
    "isExpire": false,
    "updateDate": null,
    "expireDate": "2023-03-25T16:39:12.000+0900",
    "mallReserved": null,
    "mallUserId": null,
    "buyerName": null,
    "buyerTel": null,
    "buyerEmail": null,
    "useEscrow": false,
    "currency": "KRW",
    "logoImgUrl": null,
    "language": "EN",
    "returnCharSet": null,
    "cardQuota": null,
    "cardCode": null,
    "cardShowOpt": null,
    "vbankHolder": null,
    "vbankValidHours": null,
    "vbankExpDate": null,
    "isDigital": false,
    "directReceiptType": null,
    "directReceiptNo": null,
    "disableScroll": false,
    "disableEdgeChk": false,
    "appScheme": null,
    "method": "all",
    "url": "https://sandbox-pay.nicepay.co.kr/v1/fake/pay/641d555b91ae1",
    "zidxHigher": false
}
```

<br>

## Request a checkout page
```bash
The client follows the link to the checkout page.

https://sandbox-pay.nicepay.co.kr/v1/fake/pay/641d555b91ae1
```

<img src="../image/sandbox-checkout.png" width="800px">

- This is a dummy page without actual card company authentication.  
- If you press Next, a success message will be returned in response.  
- And If you press Cancel, a random failure message will be returned in response.  

<br><br>

## Return a checkout
```bash
Content-type: application/json;charset=utf-8  

The response data will be sent to the returnUrl set in the checkout request.  

{
  "success": "true",
  "sessionId": "641d555b91ae1"
  "orderId": "641d555b91ae6",
  "authToken": "NICEUNTT0992E00E775A88C5DC13938447D237F0",
  "tid": "UT0000104m00012303241646422011",
  "clientId": "S1_ce1bb1ebebc44fe1a3f7cec976c83ea7",
  "mallReserved": "",
  "resultCode": "0000",
  "resultMsg": "정상 처리되었습니다.",
  "amount": "1004",
  "goodsName": "test",
  "channel": "pc",
  "status": "paid",
  "ediDate": "2023-03-24T16:46:42.484+0900",
  "signature": "6cd4cc86f52f15c7532f95f9be162e4f7be89292836ed02fddf6bdecb7357535",
  "paidAt": "2023-03-24T16:46:42.000+0900",
  "failedAt": "0",
  "payMethod": "card",
  "useEscrow": "false",
  "currency": "KRW",
  "approveNo": "000000",
  "couponAmt": "",
  "buyerName": "null",
  "buyerTel": "null",
  "buyerEmail": "null",
  "issuedCashReceipt": "false",
  "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m00012303241646422011",
  "mallUserId": "null",
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

## Retrieve a checkout page

<br>

```bash
GET /v1/checkout/{sessionId} 
HTTP/1.1  
Host: sandbox-api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>  
Content-type: application/json;charset=utf-8  
```

<br>

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/checkout/641d555b91ae1' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
```

<br>

```bash
Response

{
  "resultCode": "0000",
  "resultMsg": "정상 처리되었습니다.",
  "sessionId": "641d555b91ae1",
  "orderId": "641d555b91ae6",
  "clientId": "S1_ce1bb1ebebc44fe1a3f7cec976c83ea7",
  "tid": "UT0000104m00012303241646422011",
  "amount": 1004,
  "goodsName": "test",
  "returnUrl": "http://35.228.103.98/untact-sandbox/response2.php",
  "apprStatus": "cancelled",
  "skinType": null,
  "taxFreeAmt": null,
  "isExpire": true,
  "updateDate": null,
  "expireDate": "2023-03-25T16:46:36.000+0900",
  "mallReserved": null,
  "mallUserId": null,
  "buyerName": null,
  "buyerTel": null,
  "buyerEmail": null,
  "useEscrow": false,
  "currency": "KRW",
  "logoImgUrl": null,
  "language": null,
  "returnCharSet": null,
  "cardQuota": null,
  "cardCode": null,
  "cardShowOpt": null,
  "vbankHolder": null,
  "vbankValidHours": null,
  "vbankExpDate": null,
  "isDigital": false,
  "directReceiptType": null,
  "directReceiptNo": null,
  "disableScroll": false,
  "disableEdgeChk": false,
  "appScheme": null,
  "method": "all",
  "url": "https://sandbox-pay.nicepay.co.kr/v1/fake/pay/641d555b91ae1",
  "zidxHigher": false
}

```

<br><br>

## Expire a checkout page

If no expiration time is specified for the session ID, it will be accessible for up to 24 hours.   
If you want to expire the session before that, please call the expire API.  

```bash
GET /v1/checkout/{sessionId}/expire
HTTP/1.1  
Host: sandbox-api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>  
Content-type: application/json;charset=utf-8  
```

<br>

```bash
curl --location --request POST 'https://sandbox-api.nicepay.co.kr/v1/checkout/641d555b91ae2/expire' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
```

<br>

```bash
Response

{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다.",
    "sessionId": "641d555b91ae2",
    "orderId": "order-id-641d555b91ae3",
    "clientId": "S1_ce1bb1ebebc44fe1a3f7cec976c83ea7",
    "tid": null,
    "amount": 1004,
    "goodsName": "test",
    "returnUrl": "http://your-return-url.com",
    "apprStatus": "ready",
    "skinType": null,
    "taxFreeAmt": null,
    "isExpire": true,
    "updateDate": "2023-03-24T17:08:03.000+0900",
    "expireDate": "2023-03-25T17:07:53.000+0900",
    "mallReserved": null,
    "mallUserId": null,
    "buyerName": null,
    "buyerTel": null,
    "buyerEmail": null,
    "useEscrow": false,
    "currency": "KRW",
    "logoImgUrl": null,
    "language": "EN",
    "returnCharSet": null,
    "cardQuota": null,
    "cardCode": null,
    "cardShowOpt": null,
    "vbankHolder": null,
    "vbankValidHours": null,
    "vbankExpDate": null,
    "isDigital": false,
    "directReceiptType": null,
    "directReceiptNo": null,
    "disableScroll": false,
    "disableEdgeChk": false,
    "appScheme": null,
    "method": "all",
    "url": "https://sandbox-pay.nicepay.co.kr/v1/fake/pay/641d555b91ae2",
    "zidxHigher": false
}


```


<br><br>

## Transaction Status Inquiry

You can check `Transaction Status` through the response tid(transaction ID) received in the Sandbox.  


Please refer to the link for more detailed information.  
[Transaction Status Inquiry-Transaction status](/api/nicepay-api-retrieve.md#retrieve-a-transaction-with-tidtransaction-id)

<br>

### Transaction Status Inquiry with sessionId

<br>

```bash
GET /v1/payments/checkout/{sessionId} 
HTTP/1.1  
Host: sandbox-api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```
<br>

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/payments/checkout/641d555b91ae1' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
```

<br>

```bash
Response

Content-type: application/json;charset=utf-8 
{
  "resultCode": "0000",
  "resultMsg": "정상 처리되었습니다.",
  "tid": "UT0000104m00012303241646422011",
  "cancelledTid": null,
  "orderId": "641d555b91ae6",
  "ediDate": "2023-03-24T16:57:44.679+0900",
  "signature": "5d69d48856c2043bd5d09b0fb0d0a040554cb20ebe31d16699a30d63e33d563c",
  "status": "paid",
  "paidAt": "2023-03-24T16:46:42.000+0900",
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
  "approveNo": null,
  "buyerName": null,
  "buyerTel": null,
  "buyerEmail": null,
  "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m00012303241646422011",
  "mallUserId": null,
  "issuedCashReceipt": false,
  "coupon": null,
  "card": {
    "cardCode": "04",
    "cardName": "삼성",
    "cardNum": "123412******1234",
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
  "cashReceipts": null,
  "sessionId": "641d555b91ae1"
}
```


### Transaction Status Inquiry with tid

<br>

```bash
GET /v1/payments/{tid} 
HTTP/1.1  
Host: sandbox-api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```
<br>

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/payments/UT0000104m00012303241646422011' \
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
    "tid": "UT0000104m00012303241646422011",
    "cancelledTid": null,
    "orderId": "641d555b91ae6",
    "ediDate": "2023-03-24T16:55:22.018+0900",
    "signature": "c6bb1f74d4f3134cc2d01ecdfb1e04e154b6233c77cd9206fa48fd1666cbb8c5",
    "status": "paid",
    "paidAt": "2023-03-24T16:46:42.000+0900",
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
    "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m00012303241646422011",
    "mallUserId": null,
    "issuedCashReceipt": false,
    "coupon": null,
    "card": {
        "cardCode": "04",
        "cardName": "삼성",
        "cardNum": "123412******1234",
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
Host: sandbox-api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

<br>

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/payments/find/641d555b91ae6' \
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
  "tid": "UT0000104m00012303241646422011",
  "cancelledTid": null,
  "orderId": "641d555b91ae6",
  "ediDate": "2023-03-24T17:01:46.315+0900",
  "signature": "20f0f40667e4c6bda08429edac452e090818860a112cdb9d59e636b6bbe5f45d",
  "status": "paid",
  "paidAt": "2023-03-24T16:46:42.000+0900",
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
  "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m00012303241646422011",
  "mallUserId": null,
  "issuedCashReceipt": false,
  "coupon": null,
  "card": {
    "cardCode": "04",
    "cardName": "삼성",
    "cardNum": "123412******1234",
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

### Cancel with sessionId exmplae

<br>

```bash
POST /v1/payments/checkout/{sessionId}/cancel  
HTTP/1.1  
Host: sandbox-api.nicepay.co.kr  
Authorization: Basic <credentials>  or Bearer <token>  
Content-type: application/json;charset=utf-8  
```

<br>

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

```bash
Response

Content-type: application/json;charset=utf-8 

{
  "resultCode": "0000",
  "resultMsg": "정상 처리되었습니다.",
  "tid": "UT0000104m00012303241646422011",
  "cancelledTid": "UT0000104m00012303241646422011",
  "orderId": "641d555b91ae6",
  "ediDate": "2023-03-24T17:05:45.599+0900",
  "signature": "aa90669cf8d6604b8bfd3986937aadfe4ff36e196b33b805fb903d6caee9cc9f",
  "status": "cancelled",
  "paidAt": "2023-03-24T16:46:42.000+0900",
  "failedAt": "0",
  "cancelledAt": "2023-03-24T17:05:45.000+0900",
  "payMethod": "card",
  "amount": 1004,
  "balanceAmt": 0,
  "goodsName": "test",
  "mallReserved": null,
  "useEscrow": false,
  "currency": "KRW",
  "channel": "pc",
  "approveNo": null,
  "buyerName": null,
  "buyerTel": null,
  "buyerEmail": null,
  "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m00012303241646422011",
  "mallUserId": null,
  "issuedCashReceipt": false,
  "coupon": null,
  "card": {
    "cardCode": "04",
    "cardName": "삼성",
    "cardNum": "123412******1234",
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
      "tid": "UT0000104m00012303241646422011",
      "amount": 1004,
      "cancelledAt": "2023-03-24T17:05:45.000+0900",
      "reason": "고객요청",
      "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m00012303241646422011",
      "couponAmt": 0
    }
  ],
  "cashReceipts": null,
  "sessionId": "641d555b91ae1"
}
```

<br><br>

### Cancel with tid example

<br>

```bash
POST /v1/payments/{tid}/cancel  
HTTP/1.1  
Host: sandbox-api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>
Content-type: application/json;charset=utf-8
```

<br>

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