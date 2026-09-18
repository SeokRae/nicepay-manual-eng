<div align="right">
  <img src="https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Fnicepayments&count_bg=%233D7CC8&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://github.com/nicepayments">
</div>
<h3 align="center">
  🚀 NicePay For Startups
</h3>

<!-- https://github.com/denvercoder1/readme-typing-svg -->
<p align="center">
  <img src="https://readme-typing-svg.herokuapp.com?lines=Hello!+Nicepay+%7Bdevelopers%7D&center=true&width=360&height=50">
</p>
                           
<br>


### Information 
This is a common guide needed before development.  
[Client and Secret key](./info/nicepay-info-key.md) | [Firewall and Timeout](./info/nicepay-info-firewall-timeout.md) | [Basic and Bearer authentication](./info/nicepay-info-basic-token.md) | [Support environment](./info/nicepay-info-general.md) | [Sandbox](./info/nicepay-info-sandbox.md) | 
  
### API
This is a technical document that includes information about the API.  
[List of API](./api/nicepay-api-uri-list.md) | [Payment](./api/nicepay-api-payment-window-url.md) | [Recurring Payment](./api/nicepay-api-billing.md) | [Key-in Payment](./api/nicepay-api-keyin.md) | [Access token](./api/nicepay-api-access-token.md) | [Transaction Status Inquiry](./api/nicepay-api-retrieve.md) | [Cancel](./api/nicepay-api-cancel.md) | [Reconciliation](./api/nicepay-api-reconciliation.md) |  [Webhook](./api/nicepay-api-webhook.md) |

<div align="left"> 
 <a href="https://github.com/nicepayments/nicepay-node">
  <img src="https://img.shields.io/badge/node.js-339933?style=for-the-badge&logo=node.js&logoColor=white">
 </a>
 <a href="https://github.com/nicepayments/nicepay-python">
  <img src="https://img.shields.io/badge/python-3776AB?style=for-the-badge&logo=python&logoColor=white"> 
 </a>
 <a href="https://github.com/nicepayments/nicepay-ruby">
  <img src="https://img.shields.io/badge/ruby-CC342D?style=for-the-badge&logo=ruby&logoColor=white">
 </a> 
 <a href="https://github.com/nicepayments/nicepay-asp">
  <img src="https://img.shields.io/badge/asp-007396?style=for-the-badge&logo=&logoColor=white">
 </a>
 <a href="https://github.com/nicepayments/nicepay-java">
  <img src="https://img.shields.io/badge/java-F7DF1E?style=for-the-badge&logo=&logoColor=white">
 </a>  
 <a href="https://github.com/nicepayments/nicepay-php">
  <img src="https://img.shields.io/badge/php-777BB4?style=for-the-badge&logo=php&logoColor=white">
 </a>
 <a href="https://github.com/nicepayments/nicepay-dotnet">
  <img src="https://img.shields.io/badge/.net-512BD4?style=for-the-badge&logo=.net&logoColor=white">
 </a>  
</div>
👉 When you click on the development language, you can check the source code.  

<br>

### CODE
These are response and error codes.  
[HTTP status code](./code/nicepay-code.md#http-status-code) | [Card-code](./code/nicepay-code.md#card-code) | [Bank-code](./code/nicepay-code.md#bank-code) | [API Response code](./code/nicepay-code.md#api-response-code) |

<br><br>

## ⚡ Quick guide

### Getting Started

This is a ⚡ Quick guide for development.  
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
<img src="./image/payment-url.svg" width="800px">
  

If a customer send an order, please call the Checkout creation API first.   
After that, the customer can proceed with payment by accessing the URL that is returned in the response.  

<br>  

> #### ⚠️ Important  
> The Sandbox and Live domains may be different.   
> Once testing is complete, be sure to switch to the Live domain.   

<br><br>

### Step 1. Create a checkout session

- If the Create Checkout API call is successful, it will respond with a URL.
- Please refer to the [link](./api/nicepay-api-payment-window-url.md) for the request parameters of the Create Checkout API.

```bash
curl --location 'https://api.nicepay.co.kr/v1/checkout' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UjFfOTRlYjN...' \
--data '{
    "method": "cardAndEasyPay",
    "sessionId" : "unique-sessionId-001",
    "clientId": "R1_94eb3a4a30264fdba82ce0d05b465012",
    "orderId": "order-id-unique-order-001",
    "amount": 1004,
    "goodsName" : "test",
    "returnUrl": "http://your-return-url.com",
    "language" : "EN"
}'
```

### Create a checkout example response

```bash
{
    "resultCode": "0000",
    "resultMsg": "정상 처리되었습니다.",
    "sessionId": "unique-sessionId-001",
    "orderId": "order-id-unique-order-001",
    "clientId": "R1_94eb3a4a30264fdba82ce0d05b465012",
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
    "url": "https://pay.nicepay.co.kr/v1/checkout/pay/G1cKzR8pQmT3xYVn5A9Lse2f/unique-sessionId-001"
}
```

### Step 2. Redirect the customer to the checkout URL

When you access the URL that was responded, the Checkout window will be displayed, and the client will be able to make a payment.

<img src="./image/live-checkout.png" width="800px">

https://pay.nicepay.co.kr/v1/checkout/pay/G1cKzR8pQmT3xYVn5A9Lse2f/unique-sessionId-001

<br><br>

### Step 3. Receive the payment result

- When the client completes the payment, the approval information will be sent to the endpoint of the `returnUrl`

- Refer to the [Code](./code/nicepay-code.md) for the response and error codes. 

- This is delivered as a `POST` with `Content-type: application/x-www-form-urlencoded`, a flat form body, not JSON. See the [Payment Authorization Response Parameter](./api/nicepay-api-payment-window-url.md#payment-authorization-response-parameter) table for the full field list.

```bash
POST {returnUrl}
Content-type: application/x-www-form-urlencoded
```
```bash
success=true&authToken=NICEUNTT0992E00E775A88C5DC13938447D237F0&tid=nicepay01m01012303241404031098&orderId=order-id-unique-order-001&clientId=R1_94eb3a4a30264fdba82ce0d05b465012&resultCode=0000&resultMsg=정상 처리되었습니다.&amount=1004&goodsName=test&channel=pc&status=paid&ediDate=2023-03-24T14:04:16.982+0900&signature=59a05ad89bbbb6b5dda157dd31c48510f78eefdffc13ebec94f5afffa067fa4f&paidAt=2023-03-24T14:04:03.000+0900&payMethod=card&buyerEmail=test@abc.com&receiptUrl=https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0%26innerWin=Y%26TID=nicepay01m01012303241404031098&issuedCashReceipt=false&cardCode=07&cardName=현대&cardQuota=0&isInterestFree=false&cardType=1&canPartCancel=true&acquCardCode=07&acquCardName=현대&messageSource=nicepay
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

<br>