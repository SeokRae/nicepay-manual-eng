## Quick Start Guide

This guide walks through **Checkout**, the standard hosted-payment-page integration. If you need the customer's card details to stay off your own server, this is almost always the right path. If you're not sure, see [Which Integration Should I Use?](./INTEGRATION-PATHS.md) first.

Follow the guide in order to complete a Checkout test in about 10 minutes.

**Before you start**, you'll need:
- A [Client and Secret key](./info/nicepay-info-key.md) issued from the NicePay admin console
- An `Authorization` header built from those keys, see [Basic and Bearer authentication](./info/nicepay-info-basic-token.md)
- We recommend testing against the [Sandbox](./info/nicepay-info-sandbox.md) first, then switching to Live once verified

<br>

> **⚠️ Important:** If you are conducting a test in a network environment with IP restrictions, firewall configuration may be necessary to make API calls.  
> See [Firewall and Timeout](./info/nicepay-info-firewall-timeout.md).

<br>

### Over-view
<img alt="Sequence diagram: the customer places an order with the merchant server, which calls the NicePay Create Checkout API and receives a return URL, then redirects the customer to that URL to complete payment on the NicePay Checkout page" src="./image/payment-overview.svg" width="800px">
  

If a customer send an order, please call the Checkout creation API first.   
After that, the customer can proceed with payment by accessing the URL that is returned in the response.  

For the success/failure branches of a card or easy-pay checkout, see [Card and Easy Pay checkout flow](./api/nicepay-api-payment-window-url.md#card-and-easy-pay-checkout-flow).

<br>  

> **⚠️ Important:** The Sandbox and Live domains may be different.   
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

`amount` is a whole number with no decimal point. This request has no `currency`, so the payment is in Korean won: `1004` is KRW 1,004. See [Amounts and currencies](./info/nicepay-info-general.md#amounts-and-currencies).

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

Your Merchant Server redirects the customer to the `url` from the Step 1 response. The customer's browser opens the Hosted Payment Page, and the customer pays there.

Since Step 1 used `fakeAuth: "true"`, this is a dummy Sandbox page without real card company authentication: pressing Next returns a success result, and Cancel returns a random failure result.

<img alt="Sequence diagram of the full card/easy-pay payment cycle, from order to cancellation. Steps 1-7 (checkout: create checkout, redirect, Checkout page, Payment Authorization callback) are this step; steps 8-9 (cancellation, shown in orange) are covered in api/nicepay-api-cancel.md" src="./image/payment-checkout-cancel-cycle.svg" width="800px">

<br><br>

### Step 3. Receive the payment result

- When the customer finishes paying, NicePay shows a result page in the customer's browser, and the browser sends the result to your `returnUrl`. NicePay's servers do not call `returnUrl`. See [Payment Authorization](./api/nicepay-api-payment-window-url.md#payment-authorization) for what your handler returns.

- Refer to the [Code](./code/nicepay-code.md) for the response and error codes. 

- The browser sends a `POST` with `Content-type: application/x-www-form-urlencoded`, a flat form body, not JSON. See the [Payment Authorization Response Parameter](./api/nicepay-api-payment-window-url.md#payment-authorization-response-parameter) table for the full field list.

```bash
POST {returnUrl}
Content-type: application/x-www-form-urlencoded
```

Shown one field per line below for readability, after URL decoding. The real request body is a single `&`-joined string with percent-encoded values:

```bash
success=true
sessionId=unique-sessionId-001
authToken=NICEUNTT0992E00E775A88C5DC13938447D237F0
tid=UT0000104m00012303241404031098
orderId=order-id-unique-order-001
clientId=S1_ce1bb1ebebc44fe1a3f7cec976c83ea7
resultCode=0000
resultMsg=정상 처리되었습니다.
amount=1004
goodsName=test
channel=pc
status=paid
ediDate=2023-03-24T14:04:16.982+0900
signature=1e5851b3a925ea3307cde0163c1c12883fb8c218c3df16861cd9cb534b51b8b1
paidAt=2023-03-24T14:04:03.000+0900
failedAt=0
payMethod=card
useEscrow=false
currency=KRW
approveNo=000000
buyerEmail=null
receiptUrl=https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000104m00012303241404031098
issuedCashReceipt=false
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

This sample verifies with the Sandbox Secret key `13e969a77a0545799242ccc3915243d3`: the SHA-256 of `UT0000104m0001230324140403109810042023-03-24T14:04:16.982+090013e969a77a0545799242ccc3915243d3` (`tid` + `amount` + `ediDate` + Secret key) is the `signature` above.

> **⚠️ Important:** When conducting tests through the Sandbox, actual approvals will not occur.  
> Also, arbitrary values are returned in the response.  

Before your Merchant Server confirms the order, it checks the result in this order. [Verifying the payment result](./api/nicepay-api-payment-window-url.md#verifying-the-payment-result) explains each step.

1. Read the URL-decoded form fields.
2. Find your order by `orderId`. If the order is already paid, stop.
3. Check that `resultCode` is `0000`. For any other code, do not confirm the order.
4. Check `status`: `paid` means approved. `ready` means that a virtual account was issued and the customer has not paid yet: do not ship the order yet.
5. Compute `hex(sha256(tid + amount + ediDate + SecretKey))` and compare it with `signature`.
6. Compare `amount` with the amount of your order.
7. Look the payment up with [Transaction Status Inquiry](./api/nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id) (`GET /v1/payments/{tid}`) and check its `orderId`, `amount` and `status`.
8. Confirm the order and store `tid`.

<br><br>

### Next steps

- Check the transaction status anytime via [Transaction Status Inquiry](./api/nicepay-api-retrieve.md).
- Need to cancel or refund a payment? See [Cancel](./api/nicepay-api-cancel.md).
- Using virtual accounts or other methods with delayed settlement? Register a [Webhook](./api/nicepay-api-webhook.md) to catch async events like deposits.
- Ready to go live? Revisit the Sandbox vs. Live domain note above and switch your keys and base URL.
