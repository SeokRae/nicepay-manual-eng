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
[List of API](./api/nicepay-api-uri-list.md) | [Payment](./api/nicepay-api-payment-window-url.md) | [Billing](./api/nicepay-api-billing.md) | [Access token](./api/nicepay-api-access-token.md) | [Retrieve](./api/nicepay-api-retrieve.md) | [Cancel](./api/nicepay-api-cancel.md) | [Reconciliation](./api/nicepay-api-reconciliation.md) |  [Webhook](./api/nicepay-api-webhook.md) |

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
[HTTP status code](./code/nicepay-code.md#HTTP-status-code) | [Card-code](./code/nicepay-code.md#Card-code) | [Bank-code](./code/nicepay-code.md#Bank-code) | [API response code](./code/nicepay-code.md#API-response-code) |

<br><br>

## ⚡ Quick guide

### Getting Started

This is a ⚡ Quick guide for development.  
By following the guide in order, it is possible to develop a Checkout TEST in about ⏱️ 10 minutes.  

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

### Create a checkout example code

- If the Create Checkout API call is successful, it will respond with a URL.
- Please refer to the [link](./api/nicepay-api-payment-window-url.md) for the request parameters of the Create Checkout API.

```bash
curl --location --request POST 'https://pay.nicepay.co.kr/v1/checkout' \
--header 'Content-Type: application/json' \
--data-raw '{
  "clientId": "R1_94eb3a4a30264fdba82ce0d05b465012",
  "method": "card",
  "orderId": "5a639653-3154-4e79-8d5a-d7d3b64a146e",
  "amount": 1004,
  "goodsName": "test",
  "returnUrl": "http://test.com"
}'
```

### Create a checkout example response

```bash
{
    "sessionId": "NICEORD_TRK2_nicuntsx1m_2302272030343389",
    "url": "https://pay.nicepay.co.kr/v1/pay/NICEORD_TRK2_nicuntsx1m_2302272030343389",
    "resultCode": "0000",
    "resultMsg": "success"
}
```

When you access the URL that was responded, the Checkout window will be displayed, and the client will be able to make a payment.

### Payment (Approval) response example

- When the client completes the payment, the approval information will be sent to the endpoint of the `returnUrl`

- Refer to the [Code](./code/nicepay-code.md) for the response and error codes. 

```bash
POST
Content-type: application/json
```
```bash
{
  resultCode: '0000',  // Success
  resultMsg: '정상 처리되었습니다.',
  tid: 'UT0000113m01012111051714341073',
  cancelledTid: null,
  orderId: 'c74a5960-830b-4cd8-82a9-fa1ce739a18f',
  ediDate: '2021-11-05T17:14:35.150+0900',
  signature: '63b251b31c909eebef1a9f4fcc19e77bdcb8f64fc1066a29670f8627186865cd',
  status: 'paid',
  paidAt: '2021-11-05T17:14:35.000+0900',
  failedAt: '0',
  cancelledAt: '0',
  payMethod: 'card',
  amount: 1004,
  balanceAmt: 1004,
  goodsName: 'your-goods-name',
  mallReserved: null,
  useEscrow: false,
  currency: 'KRW',
  channel: 'pc',
  approveNo: '000000',
  buyerName: null,
  buyerTel: null,
  buyerEmail: null,
  receiptUrl: 'https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=UT0000113m01012111051714341073',
  mallUserId: null,
  issuedCashReceipt: false,
  coupon: null,
  card: {
    cardCode: '04',
    cardName: '삼성', // In Sandbox value is fixed (삼성:Samsung Card)
    cardNum: '12341234****1234',
    cardQuota: 0,
    isInterestFree: false,
    cardType: 'credit',
    canPartCancel: true,
    acquCardCode: '04',
    acquCardName: '삼성'
  },
  vbank: null,
  cancels: null,
  cashReceipts: null
}
```
> #### ⚠️ Important  
> When conducting tests through the Sandbox, actual approvals will not occur.  
> Also, arbitrary values are returned in the response.  
<br>