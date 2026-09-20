## ⚡ Quick Start Guide

This guide walks through **Checkout**, the standard hosted-payment-page integration. If you need the customer's card details to stay off your own server, this is almost always the right path — if you're not sure, see [Which Integration Should I Use?](./INTEGRATION-PATHS.md) first.

By following the guide in order, it is possible to develop a Checkout TEST in about ⏱️ 10 minutes.

**Before you start**, you'll need:
- A [Client and Secret key](./info/nicepay-info-key.md) issued from the NicePay admin console
- An `Authorization` header built from those keys, see [Basic and Bearer authentication](./info/nicepay-info-basic-token.md)
- We recommend testing against the [Sandbox](./info/nicepay-info-sandbox.md) first, then switching to Live once verified

<br>

> #### ⚠️ Important  
> If you are conducting a test in a network environment with IP restrictions, firewall configuration may be necessary to make API calls.  
>  👉 [Firewall and Timeout](./info/nicepay-info-firewall-timeout.md)

<br>

### Over-view
<img alt="Sequence diagram: the client sends an order to the merchant server, which calls the NicePay Create Checkout API and receives a return URL, then redirects the customer to that URL to complete payment on the NicePay Checkout page" src="./image/payment-overview.svg" width="800px">
  

If a customer send an order, please call the Checkout creation API first.   
After that, the customer can proceed with payment by accessing the URL that is returned in the response.  

For the success/failure branches of a card or easy-pay checkout, see [Card and Easy Pay checkout flow](./api/nicepay-api-payment-window-url.md#card-and-easy-pay-checkout-flow).

<br>  

> #### ⚠️ Important  
> The Sandbox and Live domains may be different.   
> Once testing is complete, be sure to switch to the Live domain.   

<br><br>

### Step 1. Create a checkout session

- If the Create Checkout API call is successful, it will respond with a URL.
- Please refer to the [link](./api/nicepay-api-payment-window-url.md) for the request parameters of the Create Checkout API.

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/checkout' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
--data '{
    "method": "cardAndEasyPay",
    "sessionId" : "unique-sessionId-001",
    "clientId": "S1_ce1bb1ebebc44fe1a3f7cec976c83ea7",
    "orderId": "order-id-unique-order-001",
    "amount": 1004,
    "goodsName" : "test",
    "returnUrl": "http://your-return-url.com",
    "language" : "EN",
    "fakeAuth": "true"
}'
```

> The `clientId`/`Authorization` above are the Sandbox test key from [Sandbox](./info/nicepay-info-sandbox.md#test-key-information); `fakeAuth: "true"` skips real card company authentication so you get an immediate test result. Once you switch to Live, drop `fakeAuth` and use your Live key instead.

### Create a checkout example response

```bash
{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다.",
    "sessionId": "unique-sessionId-001",
    "orderId": "order-id-unique-order-001",
    "clientId": "S1_ce1bb1ebebc44fe1a3f7cec976c83ea7",
    "tid": null,
    "amount": 1004,
    "goodsName": "test",
    "returnUrl": "http://your-return-url.com",
    "status": "ready",
    "skinType": null,
    "taxFreeAmt": null,
    "isExpire": false,
    "expireDate": "2023-03-25T13:58:01.000+0900",
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
    "url": "https://sandbox-pay.nicepay.co.kr/v1/fake/pay/unique-sessionId-001"
}
```

### Step 2. Redirect the customer to the checkout URL

When you access the URL that was responded, the Checkout window will be displayed, and the client will be able to make a payment.

Since Step 1 used `fakeAuth: "true"`, this is a dummy Sandbox page without real card company authentication: pressing Next returns a success result, and Cancel returns a random failure result.

<img alt="Sequence diagram of the full card/easy-pay payment cycle, from order to cancellation. Steps 1-7 (checkout: create checkout, redirect, Checkout page, Payment Authorization callback) are this step; steps 8-9 (cancellation, shown in orange) are covered in api/nicepay-api-cancel.md" src="./image/payment-checkout-cancel-cycle.svg" width="800px">

https://sandbox-pay.nicepay.co.kr/v1/fake/pay/unique-sessionId-001

<br><br>

### Step 3. Receive the payment result

- When the client completes the payment, the approval information will be sent to the endpoint of the `returnUrl`

- Refer to the [Code](./code/nicepay-code.md) for the response and error codes. 

- This is delivered as a `POST` with `Content-type: application/x-www-form-urlencoded`, a flat form body, not JSON. See the [Payment Authorization Response Parameter](./api/nicepay-api-payment-window-url.md#payment-authorization-response-parameter) table for the full field list.

```bash
POST {returnUrl}
Content-type: application/x-www-form-urlencoded
```

Shown one field per line below for readability, the real request body is a single `&`-joined query string:

```bash
success=true
authToken=NICEUNTT0992E00E775A88C5DC13938447D237F0
tid=nicepay01m01012303241404031098
orderId=order-id-unique-order-001
clientId=S1_ce1bb1ebebc44fe1a3f7cec976c83ea7
resultCode=0000
resultMsg=정상 처리되었습니다.
amount=1004
goodsName=test
channel=pc
status=paid
ediDate=2023-03-24T14:04:16.982+0900
signature=59a05ad89bbbb6b5dda157dd31c48510f78eefdffc13ebec94f5afffa067fa4f
paidAt=2023-03-24T14:04:03.000+0900
payMethod=card
buyerEmail=test@abc.com
receiptUrl=https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0%26innerWin=Y%26TID=nicepay01m01012303241404031098
issuedCashReceipt=false
cardCode=07
cardName=현대
cardQuota=0
isInterestFree=false
cardType=1
canPartCancel=true
acquCardCode=07
acquCardName=현대
messageSource=nicepay
```
> #### ⚠️ Important  
> When conducting tests through the Sandbox, actual approvals will not occur.  
> Also, arbitrary values are returned in the response.  

<br><br>

### Next steps

- Verify the amount from `signature`, then check the transaction status anytime via [Transaction Status Inquiry](./api/nicepay-api-retrieve.md).
- Need to cancel or refund a payment? See [Cancel](./api/nicepay-api-cancel.md).
- Using virtual accounts or other methods with delayed settlement? Register a [Webhook](./api/nicepay-api-webhook.md) to catch async events like deposits.
- Ready to go live? Revisit the Sandbox vs. Live domain note above and switch your keys and base URL.
