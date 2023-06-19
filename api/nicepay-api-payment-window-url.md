## Checkout

## Payment Request (Hosted Payment Page)

### Over-view
<img src="../image/payment-url.svg" width="800px">

### ⚠️ Exception handling
- Please be sure to check the amount from `signature` for tampering verification in the response message.
- If you need to verify the amount, please refer to the [Transaction Inquiry API](./nicepay-api-retrieve.md).
- If you need to cancel a payment due to a network error, please look up the transaction using [Transaction Inquiry API](./nicepay-api-retrieve.md) with 'orderId' and cancel it using the tid returned in the response. 
  

<br>

### Create Hosted Payment Page Example Code

```bash
curl --location 'https://api.nicepay.co.kr/v1/checkout' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UjFfOTRlYjN...' \
--data '{
    "method": "all",
    "sessionId" : "unique-sessionId-001",
    "orderId": "order-id-unique-order-001",
    "amount": 1004,
    "goodsName" : "test",
    "returnUrl": "http://your-return-url.com",
    "language" : "EN"
}'
```

### Create Hosted Payment Page Response Example Code

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
    "apprStatus": "ready",
    "skinType": null,
    "taxFreeAmt": null,
    "isExpire": false,
    "updateDate": null,
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
    "method": "all",
    "url": "https://pay.nicepay.co.kr/v1/checkout/pay/unique-sessionId-001",
    "zidxHigher": false,
    "messageSource": "nicepay"
}
```

### Hosted Payment Page Request Parameter <img src="https://img.shields.io/badge/-Beta version-red">

```bash
POST /v1/checkout
HTTP/1.1    
Host: api.nicepay.co.kr 
Content-type: application/json;charset=utf-8
```

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
|   sessionId    | String  |  O  | 64	  | Merchant unique session id, issued by merchant | 
|   clientId    | String  |     | 50	  | Merchant identifier, issued by NICEPAY | 
|    method     | String  |  O  | 20	  | Payment Method <br> all : Display all payment methods <br> card : local cards <br> cardBill : card billing <br> bank : bank transfer <br> directCard : directly shows card authentication page without NICE hosted page <br> vbank : virtual account  <br> cellphone : carrier billing <br>naverpayCard : Naver Pay - card (excluded Point) <br> kakaopay : Kakao Pa (Card or Money) <br>kakaopayCard : Kakao Pay - Card <br>kakaopayMoney : Kakaopay -Money <br>samsungpayCard : Samsung Pay Card <br>payco : Payco <br>ssgpay : SSGPAY <br>cardAndEasyPay : Card and Wallets, for <br>cardAndEasyPay it cannot be used together with below parameters <br>- cardCode, cardQuota, shopInterest, quotaInterest |
|    orderId    | String  |  O  | 64	  | Your unique order id<br> cannot reuse the orderid    | 
|    expireDate    | String  |    | -	  | Expiration Date of sessionId<br><br>ISO 8601  | 
|    amount     | Int  	  |  O  | 12	  | Transaction amount (only numbers are allowed) | 
|   goodsName   | String  |  O  | 40	  | Product Name<br> - doubleQuota(")와 pipLine(&brvbar;) characters are converted to '-' | 
|   returnUrl   | String  |  O  | 500	 | url for Redirect after the authentication is processed | 
| mallReserved  | String  |     | 500	 | Reserved field for the merchant<br> We recommend to use it in JSON string format.<br>double quotation mark(“) cannot be used.   | 
|  mallUserId   | String  |     | 20	  | Buyer’s ID managed by the merchant  | 
|   buyerName   | String  |     | 30	  | Buyer name 	| 
|   buyerTel    | String  |     | 40	  | Buyer phone number (number only)  | 
|  buyerEmail   | String  |     | 60	  | Buyer email | 
|   useEscrow   | Boolean |     |  -	  | true: Escrow transaction / false: general transaction(default) | 
|   currency    | String  |     |  3	  | KRW: Korean Won, USD: US Dolloar, CNY:Chinese Yuan | 
|  logoImgUrl   | String  |     | 100	 | Logo Image of the merchant in full URL<br>  ex) https://youre.site.com/image/logo.jpg<br> *(pixel)*<br>- Mobile : width 50 X height 50<br>- PC : width 94 X height 25  | 
|   language    | String  |     |  2	  | Language shown in the payment page<br> EN : English / CN : Chinese / KO : Korean (Default)| 
| returnCharSet | String  |     | 10	  | Return encoding <br>utf-8(Default) / euc-kr	 | 
|   skinType    | String  |     | 10	  | Skin Setting for Payment Page <br>red/green/purple/gray/dark | 

<br>

#### VAT Amount

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| taxFreeAmt | Int  |     | 12	  | Set the tax free amount in the total transaction amount | 

<br>

#### Cards & Wallets

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:--------:|:----------:|:-------:|:--------------|
|  cardQuata    | String   |             | 100	   | Monthly Installment period configuration<br><br>[Common]<br>Limits the installment period that the customer can choose.<br><br>[Card+PAYCO+Naver Pay]<br>- can be configured independently<br>- list installment months with a differentiater ',' <br>- for transactions paid in full, it should be set as "00" <br>- can set installment periods in 2 digits (for 3 months, it should be set as '03')<br>Ex) cardQuota=03 <br>- Explanation : only shows 3 months in installment period.<br>Minimum amount available for installment : more than 50,000KRW <br><br>[KakaoPay, Samsung Pay, SSGPAY]<br> - unavailable to set by alone. Should always be set together with cardCode.<br> - Installment period should always be one value, cannot choose 2 values.  |
| cardCode   | String |     | 100	 | Option to set specific card company<br><br>[Common]<br>Limits the cards that are available to use (Refer to the Card Company Codes)<br>- Can be configured independently <br><br>[Cards]<br>- list up the cards using differentiater ','<br>Ex1) cardCode=02<br>-> limits to only KB card (only KB card is shown in the payment page) <br>Ex2) cardCode=02,04<br>-> limits to KB and Samsung cards <br><br>[Wallets]<br>- Cannot be configured in multiple values<br>- Kakao Pay and PAYCO can set to use only cards.<br>Kakao Pay Money cannot be used for Kakao Pay, PAYCO Point cannot be used for PAYCO <br>  ex) cardCode = 06 (cannot configure multiple values)<br><br>Cards available for Wallets <br>- Samsung Pay : BC,KB,KEB Hana,Samsung,Shinhan,Hyundai,Lotte,Citi,NH,hana <br>- Kakao Pay: BC,KB,KEB Hana,Samsung,Shinhan,Hyundai,Lotte,Citi,NH,hana<br>- PAYCO : BC,KB,KEB-Hana,Samsung,Shinhan,Hyundai,Lotte,Citi,NH,hana,Hanmi,ShinsegaeHanmi,Suhyup,Shinhyup,Woori,Kwangju,Jeonbuk,Jeju,VISA,Master,JCB,Savings,UnionPay,KDB,Kakao Bank<br>- SSGPAY : BC,KB,KEB Hana,Samsung,Shinhan,Hyundai,Lotte,Citi,NH,hana,Jeonbuk,Kbank<br>- Naver Pay : BC,KB,KEB Hana,Samsung,Shinhan,Hyundai,Lotte,Citi,NH |
| cardShowOpt | String |     | 50	  | Authentication method for card companies <br><br>can set the method by card <br>- 1:Ansim Click, 2:Simple Pay, 3:App Card <br>- list card codes by differentiater '&brvbar;' <br>- Card code:authentication Type&brvbar;Card Code:authentication type<br>ex) CardShowOpt=08:3&brvbar;02:3<br>- available cards : 02(KB), 04(Samsung), 06(Shinhan), 07(Hyundai), 08(Lotte), 12(NH), 15(Woori) | 

<br>

#### Virtual account Option

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| vbankHolder | String | virtual account | 40 | Virtual account (merchant name, user name) |
| vbankValidHours | Int | | 4 | Virtual account validate time<br>- Default value D+7 days<br>Ex) If you enter 10, You can use the virtual account for 10 hours after the account is issued. |
| vbankExpDate | Int | | | Virtual Account Deposit Expiration Date (yyyyMMdd) |

<br>

#### Card bill Option
| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| bid | String | Card billing | 30 | billing key for recurring payment |

<br>

#### Phone bill Option
| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| isDigital | Int | Phone bill payment | 1 | 0: content, 1: physical |

<br>

#### Direct Option
| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| directReceiptType | String | | 20 | Cash Receipt Issuance Type<br>unPublished: Unpublished<br>individual: For personal income deduction<br>company: For business expenses proof |
| directReceiptNo | String | Naver Pay-Point | 20 | Identification information for issue of cash receipt<br>Mobile phone number (10 or 11 digits) or business operator number (10 digits)<br>* Required if directReceiptType is individual or company<br>* Enter mobile phone number if directReceiptType is individual <br>* If directReceiptType is company, enter business number.<br> * Enter only numbers without '-' |

<br>

#### Only Mobile App option
| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| appScheme  |  String  |       |  200   | Mobile App Scheme value (only for APP)<br>Ex) If the merchant App scheme is `nicepaysample`<br><br>appScheme=nicepaysample:// <br><br> If the payer completes authentication through the Nice Pay payment window in the App,It moves to the targer App passed as the appScheme value.|


### Hosted Payment Page Response Parameter 

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| sessionId | String  |  O  | 64	  | Merchant unique session id, issued by merchant | 
| orderId | String | O | 64 | Your Unique order ID |
| clientId | String | O | 50 | Client ID issued by NICEPAY |
| tid | String | | 30 | Returned when authorization is successful |
| amount | Int | O | 12 | payment amount |
| url | String |  |   | The URL to the Checkout Session. Redirect customers to this URL to take them to Checkout. |
| status | String | | 20 | Payment processing status<br><br>ready: ready to authenticatoin|
| isExpire | Boolean | |  | true : Expired <br> false : Not expired |
| expireDate | String | |  | true : Expired <br> ISO 8601 |
| expiredAt | String | |  | Expiration requested time |
| messageSource | String | |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|


And Requested Parameter's will be response

<br><br>


### Payment Authorization <img src="https://img.shields.io/badge/-Beta version-red">

If you access the URL received through the Payment Request API, the Nicepay Hosted Payment Page will be displayed.
When the payer proceeds with card authentication in the Hosted Payment Page, Nicepay will respond with approval processing result.

The payment approval response value will be delivered to the `returnUrl` endpoint provided through the 'Payment Request API'


```bash
GET /v1/checkout/{sessionId}
HTTP/1.1    
Host: api.nicepay.co.kr 
```

<br>

### Payment Authorization Response Parameter

```bash
POST
Content-type: application/x-www-form-urlencoded
```

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:------:|:--------|
| success | Boolean | | | Whether payment is successful<br><br>true: successful, false: Payment failure or authentication failure |
| authToken | String | O | 40 | Authentication TOKEN<br><br>Authentication transaction Unique Key<br>- Can be used for communication with nicepay when authentication failed. |
| tid | String | | 30 | Transaction ID<br><br>Returned when authorization is successful.<br>*If authentication fails, the TID will not be returned.|
| orderId | String | O | 64 | Your Unique order ID<br>*Not reusable|
| clientId | String |  | 50 | Client ID issued by NICEPAY |
| mallReserved | String | | 500 | Spare field for store information delivery<br>It is recommended to use JSON string format.<br>However, double quotation marks (") cannot be used |
| resultCode | String | O | 4 | Result code<br><br>0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| amount | Int | O | 12 | payment amount |
| goodsName | String | O | 40 | Product Name<br><br>Product Name (", * Special characters not allowed) |
| channel | String | O | 10 | pc:PC payment, mobile:mobile payment |
| status | String | | 20 | Payment processing status<br><br>paid: payment completed, ready: ready (virtual account number), failed: payment failed, canceled: canceled, partialCanceled: partially canceled, expired: expired<br>['paid', 'ready', 'failed', 'canceled', 'partialCanceled', 'expired'] |
| ediDate | String | | - | Creation date and time <br><br>ISO 8601 format |
| signature | String | | 256 | Forgery verification data<br><br>- Respond only with successful transactions<br>- Rule: hex(sha256(tid + amount + ediDate+ SecretKey))|
| paidAt | String | | - | When payment is complete<br><br>ISO 8601 format<br> If payment is not completed 0 |
| failedAt | String | | - | Time of payment failure<br><br>ISO 8601 format<br>If not payment failure 0 |
| payMethod | String | O | 10 | Payment method<br><br>card: credit card, vbank: virtual account, <br>naverpay=Naver Pay, kakaopay=Kakao Pay, payco=Payco, ssgpay=SSGPAY, samsungpay=Samsung Pay |
| useEscrow | Boolean | | - | Escrow transaction status<br><br>false: Normal transaction / true: Escrow transaction |
| currency | String | | 3 | Approval currency<br><br>KRW: Korean Won, USD: USD, CNY: Yuan |
| approveNo | String | | 30 | Authorization Number<br>Credit Card, Bank Transfer, Mobile Phone |
| couponAmt | Int | | 12 | Amount of instant discount applied |
| buyerName | String | | 30 | Buyer name |
| BuyerTel | String | | 40 | Buyer phone number |
| buyerEmail | String | | 60 | Buyer Email |
| issuedCashReceipt | Boolean | | - | Issuance of cash receipts<br><br>true: issued / false: not issued |
| receiptUrl | String | | 200 | Receipt URL |
| mallUserId | String | | 20 | Store User ID<br>Optional |
| cardCode | String | | 3 | Payment card issuer code |
| cardName | String | | 20 | Payment card issuer name |
| cardQuota | Int | | 3 | Installment Months<br><br>0: lump sum, 2:2 months, 3:3 months … |
| isInterestFree | Boolean | | - | Whether the merchant pays the payer's installment interest |
| cardType | String | | 1 | 1:Individual 2:Corporation 3:Overseas |
| canPartCancel | String | | - | Partial cancellation possible |
| acquCardCode | String | | 3 | Acquirer code |
| acquCardName | String | | 100 | Acquirer Name |
| vbankCode | String | | 3 | Virtual account bank code to receive deposit |
| vbankName | String | | 20 | Virtual account bank name to receive deposit |
| vbankNumber | String | | 20 | Virtual account number to receive deposit |
| vbankExpDate | String | | - | Virtual Account Expiration Date<br><br>ISO 8601 |
| vbankHolder | String | | 40 | Account holder name for issued virtual account|
| bankCode | String | O | 3 | Bank code |
| bankName | String | O | 20 | Bank name (euc-kr) |
| messageSource | String | |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|

<br><br>

### Retrieve Checkout session API <img src="https://img.shields.io/badge/-Beta version-red">

This is an API that allows you to check the status of a generated session.

<br>

### Retrieve Checkout session Request Parameter

```bash
GET /v1/checkout/{sessionId}
HTTP/1.1    
Host: api.nicepay.co.kr
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| sessionId | String  |  O  | 64	  | Merchant unique session id, issued by merchant | 

<br><br>

### Retrieve Checkout session Response Parameter

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| sessionId | String  |  O  | 64	  | Merchant unique session id, issued by merchant | 
| orderId | String | O | 64 | Your Unique order ID |
| clientId | String |  | 50 | Client ID issued by NICEPAY |
| tid | String | | 30 | Returned when authorization is successful |
| amount | Int | O | 12 | payment amount |
| url | String |  |   | The URL to the Checkout Session. Redirect customers to this URL to take them to Checkout. |
| status | String | | 20 | Payment processing status<br><br>paid: payment completed, ready: ready (virtual account number), failed: payment failed, canceled: canceled, partialCanceled: partially canceled, expired: expired<br>['paid', 'ready', 'failed', 'canceled', 'partialCanceled', 'expired']|
| isExpire | Boolean | |  | true : Expired <br> false : Not expired |
| expireDate | String | |  | true : Expired <br> ISO 8601 |
| expiredAt | String | |  | Expiration requested time |
| messageSource | String | |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|


And Requested Parameter's will be response

<br><br>

### Expire Checkout session API <img src="https://img.shields.io/badge/-Beta version-red">

This is an API for expiring a generated session.

<br>

### Expire Checkout session Request Parameter

```bash
GET /v1/checkout/{sessionId}/expire
HTTP/1.1    
Host: api.nicepay.co.kr
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| sessionId | String  |  O  | 64	  | Merchant unique session id, issued by merchant | 

<br><br>

### Expire Checkout session Response Parameter

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| resultCode | String | O | 4 | 0000 : success / other failure |
| resultMsg | String | O | 100 | Result message |
| sessionId | String  |  O  | 64	  | Merchant unique session id, issued by merchant | 
| orderId | String | O | 64 | Your Unique order ID |
| clientId | String |  | 50 | Client ID issued by NICEPAY |
| tid | String | | 30 | Returned when authorization is successful |
| amount | Int | O | 12 | payment amount |
| url | String |  |   | The URL to the Checkout Session. Redirect customers to this URL to take them to Checkout. |
| status | String | | 20 | Payment processing status<br><br>paid: payment completed, ready: ready (virtual account number), failed: payment failed, canceled: canceled, partialCanceled: partially canceled, expired: expired<br>['paid', 'ready', 'failed', 'canceled', 'partialCanceled', 'expired']|
| isExpire | Boolean | |  | true : Expired <br> false : Not expired |
| expireDate | String | |  | true : Expired <br> ISO 8601 |
| expiredAt | String | |  | Expiration requested time |
| messageSource | String | |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|

And Requested Parameter's will be response