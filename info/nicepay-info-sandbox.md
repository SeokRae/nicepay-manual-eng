## Sandbox

Sandbox and Live have a difference in domain and Client ID.   
Please use it after checking whether the issued Client ID is for Sandbox or Live.   
Sandbox responds with TEST data, and no actual approval occurs.  

Your own Sandbox keys are on the `Development Information` tab of your test merchant in the admin console, see [Where to get your keys](./nicepay-info-key.md#where-to-get-your-keys). To try the API before you sign up, use the public key in [Test key information](#test-key-information).

<br>

### Advantages of sandbox

- Immediate test and development are possible.  
- It is possible to test comfortably because actual payment does not occur.  
- A test system that does not affect the live environment.  

<br>

### Sandbox limitations

Sandbox differs from Live in these ways. [URI LIST](../api/nicepay-api-uri-list.md) shows the Sandbox availability of every endpoint.

- No payment reaches a card company or bank. NicePay simulates the approval and returns fixed values: card code `04`, card number `123412******1234`, lump-sum payment (`cardQuota` `0`), and approval number `000000`.
- Every Sandbox payment is in KRW, whatever `currency` the request sends.
- Key-in Payment is not provided. See [Key-in Payment](../api/nicepay-api-keyin.md).
- Only full cancellation works. A request with `cancelAmt` fails, see [Cancel](#cancel).
- Recurring Payment registers card tokens with `/v1/subscribe/regist` only. Naver Pay, Kakao Pay and Toss Pay recurring payments and the Bid Status Inquiry are not available. See [Recurring Payment in Sandbox](#recurring-payment-in-sandbox).
- The card event and interest-free installment inquiries return dummy data.
- With `fakeAuth` set to `true` in Create checkout, the Hosted Payment Page is a dummy page without card company authentication. See [Request a checkout page](#request-a-checkout-page).
- Transaction Search and Settlement are not available.

Webhooks work in Sandbox. You can register webhook URLs with your Sandbox key, and NicePay sends the payment event for a Sandbox payment to them. This manual has not confirmed that cancellation events are sent in Sandbox. See [Webhook](../api/nicepay-api-webhook.md).

<br>

### Using Sandbox and Live domains

Sandbox and Live use different domains for the API and the Hosted Payment Page. See the [Firewall Policy](./nicepay-info-firewall-timeout.md#firewall-policy) table for the full, authoritative list of domains and IP addresses instead of a partial copy here.

<br>

### Base URL information for Sandbox and Live 

- Sandbox : sandbox-api.nicepay.co.kr  
- Live : api.nicepay.co.kr  

See [URI LIST](../api/nicepay-api-uri-list.md) for the Sandbox availability of each endpoint, and [Sandbox limitations](#sandbox-limitations) for how Sandbox responses differ from Live.

<br>

The Hosted Payment Page runs on these domains:

- Sandbox : sandbox-pay.nicepay.co.kr  
- Live : pay.nicepay.co.kr  

Your Merchant Server does not call the Hosted Payment Page itself. NicePay returns its full address in the `url` field of the [Create checkout response](../api/nicepay-api-payment-window-url.md#hosted-payment-page-response-parameter). Redirect the customer to `url` exactly as returned, and do not build this address yourself.

<br>

### Test key information

```bash
// Test key for basic authentication
// sandbox-api.nicepay.co.kr

Client : S1_ce1bb1ebebc44fe1a3f7cec976c83ea7		
Secret : 13e969a77a0545799242ccc3915243d3
Authorization : Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM= 
```

Everyone who reads this manual shares this key. Do not use it to register, update or delete webhook URLs, because that changes the settings for every reader. Use your own Sandbox key for that. There is no public Live key: in Live, use the keys of your own Live merchant.

<br><br>

## Using Sandbox API's

<a href="../image/payment-overview.svg"><img alt="Sequence diagram: order, create checkout, checkout returned, redirect, customer opens Checkout page, result page after approval, browser posts to returnUrl" src="../image/payment-overview.svg" width="800px"></a>

The steps below create a checkout session and open the Hosted Payment Page in Sandbox.

1. Your Merchant Server creates a `sessionId` and an `orderId` for the order. NicePay does not generate them.
2. Your Merchant Server calls Create checkout (`POST /v1/checkout`) with these two values and the other request parameters.
3. NicePay returns the Hosted Payment Page address in the `url` field of the response. Your Merchant Server redirects the customer to `url` exactly as returned. Do not build this address yourself.
4. The customer pays on the Hosted Payment Page, and the customer's browser sends the payment result to your `returnUrl`. With `fakeAuth` set to `true`, the page is a dummy page that skips card company authentication. Without it, the customer goes through the regular card company authentication, and NicePay then simulates the approval.


Please refer to the link for more detailed information.  
[Payment Request (Hosted Payment Page)](../api/nicepay-api-payment-window-url.md) 

<br>

Use the same Sandbox test key shown in [Test key information](#test-key-information) above.

<br>

## Create a checkout

Your Merchant Server creates `sessionId` (up to 256 bytes) and `orderId` (up to 64 bytes). Each must be unique for your merchant account, with no time limit: a used `orderId` fails with [`U112`](../code/nicepay-code.md#api-response-code), and a used `sessionId` fails with [`U324`](../code/nicepay-code.md#api-response-code), even when the earlier session expired or failed. Replace the example values below with your own. See [Hosted Payment Page Request Parameter](../api/nicepay-api-payment-window-url.md#hosted-payment-page-request-parameter).

<br>

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/checkout' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
--data '{
    "sessionId" : "641d555b91ae1",
    "orderId" : "641d555b91ae6",    
    "method" : "cardAndEasyPay",    
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
    "status": "ready",
    "skinType": null,
    "taxFreeAmt": null,
    "isExpire": false,
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
    "appScheme": null,
    "method": "cardAndEasyPay",
    "url": "https://sandbox-pay.nicepay.co.kr/v1/fake/pay/641d555b91ae1"
}
```

<br>

## Request a checkout page
```bash
The customer follows the link to the checkout page.

https://sandbox-pay.nicepay.co.kr/v1/fake/pay/641d555b91ae1
```

<a href="../image/sandbox-checkout.png"><img alt="Screenshot of the Sandbox checkout page, a dummy version without real card company authentication" src="../image/sandbox-checkout.png" width="715px"></a>

- This session was created with `fakeAuth` set to `true`, so this is a dummy page without actual card company authentication.  
- If you press Next, a success message will be returned in response.  
- And If you press Cancel, a random failure message will be returned in response.  

<br><br>

## Payment result (returnUrl callback)
```bash
POST {returnUrl}
Content-type: application/x-www-form-urlencoded
```

The customer's browser sends the result to the `returnUrl` set in the checkout request, as a flat `&`-joined form body. Shown one field per line below for readability, after URL decoding:

```bash
success=true
sessionId=641d555b91ae1
orderId=641d555b91ae6
authToken=NICEUNTT0992E00E775A88C5DC13938447D237F0
tid=UT0000104m00012303241646422011
clientId=S1_ce1bb1ebebc44fe1a3f7cec976c83ea7
mallReserved=
resultCode=0000
resultMsg=정상 처리되었습니다.
amount=1004
goodsName=test
channel=pc
status=paid
ediDate=2023-03-24T16:46:42.484+0900
signature=6cd4cc86f52f15c7532f95f9be162e4f7be89292836ed02fddf6bdecb7357535
paidAt=2023-03-24T16:46:42.000+0900
failedAt=0
payMethod=card
useEscrow=false
currency=KRW
approveNo=000000
couponAmt=
buyerName=null
buyerTel=null
buyerEmail=null
issuedCashReceipt=false
receiptUrl=https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m00012303241646422011
mallUserId=null
cardCode=04
cardName=삼성
cardQuota=0
isInterestFree=false
cardType=credit
canPartCancel=true
acquCardCode=04
acquCardName=삼성
messageSource=nicepay
```

This sample verifies with the Sandbox Secret key in [Test key information](#test-key-information). See the worked example in [Verifying the payment result](../api/nicepay-api-payment-window-url.md#verifying-the-payment-result).

Through the page below, you can easily check the flow when conducting a test  
https://nicepaytest.link/checkout/sandbox-redirect.php  
https://nicepaytest.link/checkout/sandbox-redirect-fake.php  

<br><br>

## Retrieve a checkout page

Use it if you need to check the status of the session ID.  

[Retrieve a checkout parameter](../api/nicepay-api-payment-window-url.md#retrieve-checkout-session-api) 

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
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' 
```

<br>

This example response was captured after the payment was cancelled (see [Cancel](#cancel) below). It comes from a session that was created with a different `returnUrl` and `language`. That is why `status`, `returnUrl`, `language` and `expireDate` differ from the [Return a checkout](#return-a-checkout) example.

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
  "returnUrl": "https://nicepaytest.link/checkout/sandbox-response.php",
  "status": "cancelled",
  "skinType": null,
  "taxFreeAmt": null,
  "isExpire": true,
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
  "appScheme": null,
  "method": "cardAndEasyPay",
  "url": "https://sandbox-pay.nicepay.co.kr/v1/fake/pay/641d555b91ae1"
}

```

<br><br>

## Expire a checkout page

If no expiration time is specified for the session ID, it will be accessible for up to 24 hours.   
If you want to expire the session before that, please call the expire API.  

[Expire a checkout parameter](../api/nicepay-api-payment-window-url.md#expire-checkout-session-api)



```bash
POST /v1/checkout/{sessionId}/expire
HTTP/1.1  
Host: sandbox-api.nicepay.co.kr 
Authorization: Basic <credentials>  or Bearer <token>  
Content-type: application/json;charset=utf-8  
```

<br>

This example expires a different session (`641d555b91ae2`) that has not been paid, not the session used in the examples above.

```bash
curl --location --request POST 'https://sandbox-api.nicepay.co.kr/v1/checkout/641d555b91ae2/expire' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' 
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
    "status": "ready",
    "skinType": null,
    "taxFreeAmt": null,
    "isExpire": true,
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
    "appScheme": null,
    "method": "cardAndEasyPay",
    "url": "https://sandbox-pay.nicepay.co.kr/v1/fake/pay/641d555b91ae2"
}


```


<br><br>

## Transaction Status Inquiry

You can check `Transaction Status` through the response id(sessionId or transaction ID) received in the Sandbox.  


Please refer to the link for more detailed information.  
[Transaction Status Inquiry-Transaction status](../api/nicepay-api-retrieve.md#transaction-status-inquiry-with-sessionid)

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
[Transaction Status Inquiry-orderId](../api/nicepay-api-retrieve.md#transaction-status-inquiry-with-orderid)

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

In Sandbox, only full cancellation is possible. To cancel, leave `cancelAmt` out of the request. Sandbox treats any request that contains `cancelAmt` as a partial cancellation and rejects it, even when the value equals the full payment amount. The error is [`U128`](../code/nicepay-code.md#api-response-code) unless an earlier check fails first. Partial cancellation works only in Live. See `cancelAmt` in [Cancel Request parameter (with tid)](../api/nicepay-api-cancel.md#cancel-request-parameter-with-tid).

In Sandbox, the `orderId` in the cancel response is the `orderId` of the payment (`641d555b91ae6` below), not the `orderId` sent in the cancel request.

Please refer to the link for more detailed information.  
[Cancel request](../api/nicepay-api-cancel.md#cancel-request-parameter-with-sessionid) 

<br>

### Cancel with sessionId example

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

<br><br>

## Recurring Payment in Sandbox

Recurring Payment works in Sandbox for card tokens. Call the endpoints below on `sandbox-api.nicepay.co.kr` with a Sandbox key, and encrypt `encData` with the Sandbox Secret key of that key. The request and response fields are the same as in [Recurring Payment](../api/nicepay-api-billing.md).

1. **Register a token** with [`POST /v1/subscribe/regist`](../api/nicepay-api-billing.md#create-tokenbid-request-parameter). Sandbox decrypts `encData` and checks that the fields your merchant's level requires are present and not empty, as in [encData Field Details](../api/nicepay-api-billing.md#encdata-field-details): a missing field fails with [`U317`](../code/nicepay-code.md#api-response-code), and `encData` that it cannot decrypt fails with [`F101`](../code/nicepay-code.md#api-response-code). Sandbox does not check the card number itself or whether the expiry date is in the future, and there is no list of test card numbers. It does not store the card you send: the response has a new `bid` and `cardCode` `04` whatever card you sent.
2. **Charge the token** with [`POST /v1/subscribe/{bid}/payments`](../api/nicepay-api-billing.md#recurring-payment---authorization-request-parameter) and the `bid` from step 1. As in Live, a used `orderId` fails with [`U112`](../code/nicepay-code.md#api-response-code). An installment of more than one month (`cardQuota` greater than `1`) with an `amount` below 50,000 fails with [`3024`](../code/nicepay-code.md#api-response-code). The card fields in the response are the fixed Sandbox values in [Sandbox limitations](#sandbox-limitations).
3. **Delete the token** with [`POST /v1/subscribe/{bid}/expire`](../api/nicepay-api-billing.md#delete-tokenbid-request-parameter). After that, a charge or a delete with the same `bid` fails with [`U309`](../code/nicepay-code.md#api-response-code).

The [Bid Status Inquiry](../api/nicepay-api-billing.md#bid-status-inquiry) does not work in Sandbox, and Sandbox cannot register Naver Pay, Kakao Pay or Toss Pay recurring tokens through Checkout.
