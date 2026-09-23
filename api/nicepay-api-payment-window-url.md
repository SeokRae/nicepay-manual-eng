## Checkout

## Payment Request (Hosted Payment Page)

New to Checkout? The [Quick Start Guide](../QUICKSTART.md) walks through this end to end. If you just need the reference:

**Before you start**, you'll need:
- A [Client and Secret key](../info/nicepay-info-key.md) issued from the NicePay admin console
- An `Authorization` header built from those keys, see [Basic and Bearer authentication](../info/nicepay-info-basic-token.md)
- We recommend testing against the [Sandbox](../info/nicepay-info-sandbox.md) first, then switching to Live once verified

<br>

### Over-view
<a href="../image/payment-overview.svg"><img alt="Sequence diagram: order, create checkout, checkout returned, redirect, customer opens Checkout page, result page after approval, browser posts to returnUrl" src="../image/payment-overview.svg" width="800px"></a>

<br>

### Card and Easy Pay checkout flow
<a href="../image/payment-checkout-cancel-cycle.svg"><img alt="Sequence diagram: order, create checkout, redirect, pay, result page, browser posts to returnUrl, status inquiry, confirm order; Cancellation: cancel, result" src="../image/payment-checkout-cancel-cycle.svg" width="800px"></a>

1. The customer sends an order to the Merchant Server.
2. The Merchant Server calls Create Checkout (`POST /v1/checkout`) with `method: cardAndEasyPay`.
3. NicePay responds with `resultCode: 0000`, the checkout `url`, and `status: ready`.
4. The Merchant Server redirects the customer to that `url`.
5. The customer opens the Hosted Payment Page and selects a card or an easy-pay wallet.
6. The customer authenticates and approves the payment.
7. NicePay returns a result page to the customer's browser.
8. The customer's browser posts the payment result to your `returnUrl` as a form (`POST {returnUrl}`): `success: true` and `status: paid` on success, or `success: false` and `status: failed` on failure.
9. The Merchant Server calls [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id) (`GET /v1/payments/{tid}`, available in Sandbox and Live).
10. NicePay returns the payment status (`orderId`, `amount`, `status: paid`). The Merchant Server verifies `resultCode`, `signature`, `amount`, and `status`, then confirms the order. See [Verifying the payment result](#verifying-the-payment-result) for the full checks.
11. Later, the Merchant Server can cancel the payment with `POST /v1/payments/{tid}/cancel`, sending `reason`, `orderId`, and an optional `cancelAmt` (omit it for a full cancellation, set it for a partial one).
12. NicePay returns the cancellation result: `resultCode: 0000` with `status: cancelled` or `partialCancelled`, or a `resultCode` other than `0000` on failure.

**Steps 1-8 above** are the same flow shown in [Over-view](#over-view), detailed for `method: cardAndEasyPay` (credit card and easy-pay wallets). After the customer selects a card or a wallet on the Hosted Payment Page, the field set returned in the `returnUrl` callback varies by `payMethod`; see [Payment Authorization Response Parameter](#payment-authorization-response-parameter). Steps 11-12, bracketed as Cancellation in the diagram, can follow later; see [Cancel](./nicepay-api-cancel.md) for that part of the cycle. Before you confirm an order, run the checks in [Verifying the payment result](#verifying-the-payment-result).

> **⚠️ Important:** In Sandbox with `fakeAuth: "true"`, pressing Next on the Checkout page always returns a success result and Cancel returns a random failure result: this is a Sandbox-only shortcut, not real Live authentication/failure behavior.  

<br>

### Create Hosted Payment Page Example Code

This example calls Sandbox (`sandbox-api.nicepay.co.kr`) with the public Sandbox key from [Test key information](../info/nicepay-info-sandbox.md#test-key-information). For Live, use `api.nicepay.co.kr` and your own Live key.

```bash
curl --location 'https://sandbox-api.nicepay.co.kr/v1/checkout' \
--header 'Content-Type: application/json' \
--header 'Authorization: Basic UzFfY2UxYmIxZWJlYmM0NGZlMWEzZjdjZWM5NzZjODNlYTc6MTNlOTY5YTc3YTA1NDU3OTkyNDJjY2MzOTE1MjQzZDM=' \
--data '{
    "method": "cardAndEasyPay",
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
    "url": "https://sandbox-pay.nicepay.co.kr/v1/checkout/pay/G1cKzR8pQmT3xYVn5A9Lse2f/unique-sessionId-001",
    "messageSource": "nicepay"
}
```

### Hosted Payment Page Request Parameter

```bash
POST /v1/checkout
HTTP/1.1    
Host: api.nicepay.co.kr 
Content-type: application/json;charset=utf-8
```

Required: Yes = always send; No = optional; Conditional = send in the case stated in Description. Bytes = maximum length in bytes.

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
|   `sessionId`    | String  |  Yes  | 256	  | Merchant unique session id, issued by merchant | 
|   `clientId`    | String  | No | 50	  | Merchant identifier, issued by NICEPAY | 
|    `method`     | String  |  Yes  | 20	  | Payment Method <br> card : credit and debit cards <br> cardBill : card billing <br> bank : bank transfer <br> directCard : directly shows card authentication page without the Hosted Payment Page <br> vbank : virtual account  <br> cellphone : carrier billing <br>naverpayCard : Naver Pay - card (excluded Point) <br>naverpayPoint : Naver Pay - point <br>naverCardBill : Naver Pay recurring payment - card (billing key) <br>naverPointBill : Naver Pay recurring payment - point (billing key) <br> kakaopay : Kakao Pay (Card or Money) <br>kakaopayCard : Kakao Pay - Card <br>kakaopayMoney : Kakao Pay - Money <br>kakaoBill : Kakao Pay recurring payment (billing key) <br>samsungpayCard : Samsung Pay Card <br>tosspay : Toss Pay (Card or Money) <br>tosspayCard : Toss Pay - Card <br>tosspayMoney : Toss Pay - Money <br>tosspayBill : Toss Pay recurring payment (billing key) <br>payco : Payco <br>ssgpay : SSGPAY <br>cardAndEasyPay : Card and Wallets, for <br>cardAndEasyPay it cannot be used together with below parameters <br>- cardCode, cardQuota |
|    `orderId`    | String  |  Yes  | 64	  | Your unique order id<br> cannot reuse the orderid    | 
|    `expireDate`    | String  | No | -	  | Expiration date and time of the session<br>Default: 1 day after NicePay creates the session, or 1 hour for some merchant accounts. The `expireDate` of the response shows the time that applies<br>Format: see [Dates in requests](../info/nicepay-info-general.md#dates-in-requests)  | 
|    `amount`     | Int  	  |  Yes  | 12	  | Transaction amount<br>Whole number with no decimal point. See [Amounts and currencies](../info/nicepay-info-general.md#amounts-and-currencies) | 
|   `goodsName`   | String  |  Yes  | 100	  | Product Name<br> - doubleQuota(") and pipLine(&brvbar;) characters are converted to '-'<br> - For `tosspayBill` and `kakaoBill`, anything past 40 bytes is cut off before the payment network sees it, with no error. Keep the name within 40 bytes (about 13 Korean characters in UTF-8) for those two methods. | 
|   `returnUrl`   | String  |  Yes  | 2500	 | url for Redirect after the authentication is processed | 
| `mallReserved`  | String  | No | 500	 | Reserved field for the merchant<br> We recommend to use it in JSON string format.<br>double quotation mark(“) cannot be used.   | 
|  `mallUserId`   | String  | No | 20	  | Buyer’s ID managed by the merchant  | 
|   `buyerName`   | String  | No | 30	  | Buyer name 	| 
|   `buyerTel`    | String  | No | 40	  | Buyer phone number (number only)  | 
|  `buyerEmail`   | String  | No | 60	  | Buyer email | 
|   `useEscrow`   | Boolean | No |  -	  | true: Escrow transaction / false: general transaction(default) | 
|   `currency`    | String  | No |  3	  | Currency of `amount`<br>`KRW`: Korean won (default) / `USD`: US dollar / `CNY`: Chinese yuan<br>Upper case only. Before you use `USD` or `CNY`, see [Amounts and currencies](../info/nicepay-info-general.md#amounts-and-currencies) and [Accepting overseas customers](../info/nicepay-info-general.md#accepting-overseas-customers) | 
|  `logoImgUrl`   | String  | No | 100	 | Logo Image of the merchant in full URL<br>  ex) https://youre.site.com/image/logo.jpg<br> *(pixel)*<br>- Mobile : width 50 X height 50<br>- PC : width 94 X height 25  | 
|   `language`    | String  | No |  2	  | Language shown in the payment page<br> EN : English / CN : Chinese / KO : Korean (Default)| 
| `returnCharSet` | String  | No | 10	  | Character set of the result page that sends the `returnUrl` callback: `utf-8` (default) or `euc-kr`<br>Keep `utf-8`. EUC-KR cannot represent many non-Korean characters, such as accented Latin letters (é, ñ) and simplified Chinese, in fields such as `buyerName` and `goodsName`<br>This value does not change the Create checkout response, which is always UTF-8, or the Bytes limits, which NicePay counts in UTF-8. A value other than `utf-8`, `euc-kr`, `UTF-8` or `EUC-KR` fails with [`U132`](../code/nicepay-code.md#api-response-code)	 | 
|   `skinType`    | String  | No | 10	  | Skin Setting for Payment Page <br>red/green/purple/gray/dark | 
|   `fakeAuth`    | Boolean | No |  -	  | Sandbox testing only<br>`true`: the returned `url` opens a dummy Hosted Payment Page without card company authentication, see [Request a checkout page](../info/nicepay-info-sandbox.md#request-a-checkout-page)<br>Omitted or `false` (default): the regular Hosted Payment Page. In Sandbox the customer still goes through card company authentication, and NicePay simulates the approval<br>Never send it in Live. NicePay does not reject it there, but the returned `url` then points to the dummy page instead of the regular Hosted Payment Page<br>The response does not echo this field | 

<br>

#### VAT Amount

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| `taxFreeAmt` | Int  | No | 12	  | Tax-free part of `amount`<br>Whole number with no decimal point, not greater than `amount`: a larger value fails with [`U327`](../code/nicepay-code.md#api-response-code). See [Tax breakdown](../info/nicepay-info-general.md#tax-breakdown) | 

<br>

#### Cards & Wallets

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:--------:|:----------:|:-------:|:--------------|
|  `cardQuota`    | String   | No | 100	   | Monthly Installment period configuration<br><br>[Common]<br>Limits the installment period that the customer can choose.<br><br>[Card+PAYCO+Naver Pay]<br>- can be configured independently<br>- list installment months with a differentiater ',' <br>- for transactions paid in full, it should be set as "00" <br>- can set installment periods in 2 digits (for 3 months, it should be set as '03')<br>Ex) cardQuota=03 <br>- Explanation : only shows 3 months in installment period.<br>Minimum amount for installments: KRW 50,000 or more. NicePay compares the number in `amount` with 50,000 and does not look at `currency`. <br><br>[KakaoPay, Samsung Pay, SSGPAY]<br> - unavailable to set by alone. Should always be set together with cardCode.<br> - Installment period should always be one value, cannot choose 2 values.  |
| `cardCode`   | String | No | 100	 | Option to set specific card company<br><br>[Common]<br>Limits the cards that are available to use (Refer to the Card Company Codes)<br>- Can be configured independently <br><br>[Cards]<br>- list up the cards using differentiater ','<br>Ex1) cardCode=02<br>-> limits to only KB card (only KB card is shown in the payment page) <br>Ex2) cardCode=02,04<br>-> limits to KB and Samsung cards <br><br>[Wallets]<br>- Cannot be configured in multiple values<br>- Kakao Pay and PAYCO can set to use only cards.<br>Kakao Pay Money cannot be used for Kakao Pay, PAYCO Point cannot be used for PAYCO <br>  ex) cardCode = 06 (cannot configure multiple values)<br><br>Cards available for Wallets <br>- Samsung Pay : BC,KB,KEB Hana,Samsung,Shinhan,Hyundai,Lotte,Citi,NH,hana <br>- Kakao Pay: BC,KB,KEB Hana,Samsung,Shinhan,Hyundai,Lotte,Citi,NH,hana<br>- PAYCO : BC,KB,KEB-Hana,Samsung,Shinhan,Hyundai,Lotte,Citi,NH,hana,Hanmi,ShinsegaeHanmi,Suhyup,Shinhyup,Woori,Kwangju,Jeonbuk,Jeju,VISA,Master,JCB,Savings,UnionPay,KDB,Kakao Bank<br>- SSGPAY : BC,KB,KEB Hana,Samsung,Shinhan,Hyundai,Lotte,Citi,NH,hana,Jeonbuk,Kbank<br>- Naver Pay : BC,KB,KEB Hana,Samsung,Shinhan,Hyundai,Lotte,Citi,NH |
| `cardShowOpt` | String | No | 50	  | Authentication method for card companies <br><br>can set the method by card <br>- 1:Ansim Click, 2:Simple Pay, 3:App Card <br>- list card codes by differentiater '&brvbar;' <br>- Card code:authentication Type&brvbar;Card Code:authentication type<br>ex) CardShowOpt=08:3&brvbar;02:3<br>- available cards : 02(KB), 04(Samsung), 06(Shinhan), 07(Hyundai), 08(Lotte), 12(NH), 15(Woori) | 

<br>

#### Virtual account Option

> **⚠️ Important:** For `method` `vbank`, the `returnUrl` callback arrives when NicePay issues the virtual account, before the customer pays. It has `resultCode` `0000` and `status` `ready`. Do not ship the order yet.  
> Register a [webhook](./nicepay-api-webhook.md#delivery-of-webhook) for `vbank` or `all`, and confirm the order when the deposit event (`status` `paid`) arrives. Without a registered webhook URL, NicePay sends no deposit event, and your Merchant Server has to look the payment up with [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id) until `status` is `paid`.  
> Read the account number to show the customer from the `vbank` object of Transaction Status Inquiry. The callback's `vbankNumber` can be empty.  

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| `vbankHolder` | String | Conditional | 40 | Virtual account (merchant name, user name)<br>Required when `method` is `vbank` |
| `vbankValidHours` | Int | No | 4 | Hours that the virtual account stays open, counted from when the customer opens the Hosted Payment Page<br>A positive whole number of up to 4 digits. 0 or less fails with [`U339`](../code/nicepay-code.md#api-response-code), and a value that is not a number fails with [`U329`](../code/nicepay-code.md#api-response-code)<br>In Live, this field takes precedence when you send both `vbankValidHours` and `vbankExpDate`. When you send neither, the payment network's default applies, and this manual does not confirm it<br>In Sandbox, `vbankExpDate` takes precedence, and with neither field the account closes 7 days after NicePay issues it |
| `vbankExpDate` | String | No | | Deposit deadline of the virtual account<br>Send a date and a time in Korea Standard Time (KST), for example `2023-03-25T23:59`. See [Dates in requests](../info/nicepay-info-general.md#dates-in-requests) for the accepted forms |

<br>

#### Phone bill Option

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| `isDigital` | Boolean | Conditional | 5 | false: content, true: physical<br>Required when `method` is `cellphone` |

<br>

#### Direct Option

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| `directReceiptType` | String | No | 20 | Cash Receipt Issuance Type<br>unPublished: Unpublished<br>individual: For personal income deduction<br>company: For business expenses proof |
| `directReceiptNo` | String | Conditional | 20 | Identification information for issue of cash receipt<br>Mobile phone number (10 or 11 digits) or business operator number (10 digits)<br>* Required when the payment method is Naver Pay point<br>* Required if directReceiptType is individual or company<br>* Enter mobile phone number if directReceiptType is individual <br>* If directReceiptType is company, enter business number.<br> * Enter only numbers without '-' |

<br>

#### Only Mobile App option

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:-------:|:--------------|
| `appScheme`  |  String  | No |  200   | Mobile App Scheme value (only for APP)<br>Ex) If the merchant App scheme is `nicepaysample`<br><br>appScheme=nicepaysample:// <br><br> If the customer completes authentication through the Hosted Payment Page in the App,It moves to the targer App passed as the appScheme value.|


### Hosted Payment Page Response Parameter 

Required: Yes = has a non-empty value in every response whose `resultCode` is `0000`; No = can be `null`, empty, or left out. For a field of an object or array, Yes applies whenever that object or array element is present.

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `sessionId` | String  | Yes | 256	  | Merchant unique session id, issued by merchant | 
| `orderId` | String | Yes | 64 | Your Unique order ID |
| `clientId` | String | Yes | 50 | Client ID issued by NICEPAY |
| `tid` | String | No | 30 | Returned when authorization is successful |
| `amount` | Int | Yes | 12 | payment amount |
| `url` | String | Yes |   | The URL to the Checkout Session. Redirect customers to this URL to take them to Checkout. |
| `status` | String | Yes | 20 | Status of the checkout session<br><br>ready: created, not paid yet<br>pending: the customer authenticated, and approval is in progress<br>paid: approved. For a virtual account, the account was issued<br>failed: authentication or approval failed<br>closed: the customer left the Hosted Payment Page<br>cancelled: cancelled<br>partialCancelled: partially cancelled<br>['ready', 'pending', 'paid', 'failed', 'closed', 'cancelled', 'partialCancelled']<br><br>A virtual account deposit does not change this value. To learn about the deposit, use the [webhook](./nicepay-api-webhook.md#delivery-of-webhook) or [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-sessionid). In Sandbox with `fakeAuth: "true"`, the session takes the `status` of the payment, so a virtual account session shows `ready` after the account is issued.<br><br>Expiration is not represented as a `status` value; check the separate `isExpire` boolean field instead. |
| `isExpire` | Boolean | Yes |  | true : Expired <br> false : Not expired |
| `expireDate` | String | Yes |  | ISO 8601 (session validity period) |
| `messageSource` | String | Yes |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|


Parameters you requested are also echoed back in the response. `vbankExpDate` comes back in a different format, see [Dates in responses](../info/nicepay-info-general.md#dates-in-responses).

<br><br>


### Payment Authorization

Your Merchant Server redirects the customer to the `url` from the Create checkout response, and the customer's browser opens the Hosted Payment Page. After the customer authenticates, NicePay approves the payment. NicePay then returns a result page to the customer's browser, and the browser sends the result to the `returnUrl` of the session as a form `POST`. NicePay's servers do not call `returnUrl`.

```bash
POST {returnUrl}
HTTP/1.1
Content-type: application/x-www-form-urlencoded
```

- The request comes from the customer's browser, not from NicePay's IP addresses. Do not limit `returnUrl` to the webhook IP addresses in [Firewall Policy](../info/nicepay-info-firewall-timeout.md#firewall-policy).
- NicePay does not read the response of your `returnUrl` handler and sets no time limit for it. The customer's browser shows your response, so return your order result page.
- NicePay does not retry the callback. If the customer closes the browser before it is sent, the approved payment still stands. Your Merchant Server then learns the result from the [webhook](./nicepay-api-webhook.md) or from [Transaction Status Inquiry](./nicepay-api-retrieve.md).
- Check every callback as described in [Verifying the payment result](#verifying-the-payment-result) before you confirm the order.

<br>

### Payment Authorization Response Parameter

```bash
POST
Content-type: application/x-www-form-urlencoded
```

The values below are shown after URL decoding. In the request body, every value is percent-encoded text.

Dates and times that NicePay returns are in Korea Standard Time (KST, UTC+9), for example `2023-03-24T14:04:16.982+0900`. See [Dates in responses](../info/nicepay-info-general.md#dates-in-responses) for how to parse them.

| Parameter     |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:---------:|:----------:|:------:|:--------|
| `success` | Boolean | Yes | | Whether payment is successful<br><br>`true` when `resultCode` is `0000`, otherwise `false` |
| `authToken` | String | Yes | 40 | Authentication TOKEN<br><br>Authentication transaction Unique Key<br>- Can be used for communication with nicepay when authentication failed. |
| `tid` | String | Yes | 30 | Transaction ID<br><br>Returned when authorization is successful.<br>*If authentication fails, the TID will not be returned.|
| `orderId` | String | Yes | 64 | Your Unique order ID<br>*Not reusable<br>Find your order by this value|
| `sessionId` | String | No | 256 | Checkout session ID<br>Sent in Sandbox when the session was created with `fakeAuth: "true"`. Not guaranteed in Live: find your order by `orderId` |
| `clientId` | String | Yes | 50 | Client ID issued by NICEPAY |
| `mallReserved` | String | No | 500 | Spare field for store information delivery<br>It is recommended to use JSON string format.<br>However, double quotation marks (") cannot be used |
| `resultCode` | String | Yes | 4 | Result code<br><br>0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `amount` | Int | Yes | 12 | payment amount |
| `goodsName` | String | Yes | 40 | Product Name<br><br>Product Name (", * Special characters not allowed) |
| `channel` | String | No | 10 | pc:PC payment, mobile:mobile payment |
| `status` | String | Yes | 20 | Result of the payment<br><br>paid: approved<br>ready: virtual account issued, not paid yet<br>failed: declined, authentication failed, or cancelled by NicePay after an error (`U504`)<br>closed: the customer cancelled or closed the Hosted Payment Page<br>['paid', 'ready', 'failed', 'closed'] |
| `ediDate` | String | Yes | - | Creation date and time <br><br>ISO 8601 format<br>Use it exactly as received when you verify `signature` |
| `signature` | String | Yes | 256 | Forgery verification data<br><br>Rule: hex(sha256(tid + amount + ediDate + SecretKey)), see [Verifying the payment result](#verifying-the-payment-result)<br>Covers only `tid`, `amount` and `ediDate`. Verify it only when `resultCode` is `0000`: on a failure it can hold a value that this rule does not reproduce |
| `paidAt` | String | Yes | - | When payment is complete<br><br>ISO 8601 format<br> If payment is not completed 0 |
| `failedAt` | String | Yes | - | Time of payment failure<br><br>ISO 8601 format<br>If not payment failure 0 |
| `payMethod` | String | Yes | 10 | Payment method<br><br>card: credit card, vbank: virtual account, bank: account transfer, cellphone: mobile phone, <br>naverpay=Naver Pay, kakaopay=Kakao Pay, payco=Payco, ssgpay=SSGPAY, samsungpay=Samsung Pay, tosspay=Toss Pay |
| `useEscrow` | Boolean | No | - | Escrow transaction status<br><br>false: Normal transaction / true: Escrow transaction |
| `currency` | String | Yes | 3 | Approval currency<br><br>KRW: Korean Won, USD: USD, CNY: Yuan |
| `approveNo` | String | No | 30 | Authorization Number<br>Credit Card, Bank Transfer, Mobile Phone |
| `couponAmt` | Int | No | 12 | Amount of instant discount applied<br>Can be empty or the text `null` when no discount was applied |
| `buyerName` | String | No | 30 | Buyer name |
| `buyerTel` | String | No | 40 | Buyer phone number |
| `buyerEmail` | String | No | 60 | Buyer Email |
| `issuedCashReceipt` | Boolean | Yes | - | Issuance of cash receipts<br><br>true: issued / false: not issued |
| `receiptUrl` | String | No | 200 | Receipt URL |
| `mallUserId` | String | No | 20 | Store User ID<br>Optional |
| `cardCode` | String | No | 3 | Payment card issuer code |
| `cardName` | String | No | 20 | Payment card issuer name |
| `cardQuota` | Int | No | 3 | Installment Months<br><br>0: lump sum, 2:2 months, 3:3 months … |
| `isInterestFree` | Boolean | No | - | Whether the merchant pays the customer's installment interest |
| `cardType` | String | No | 6 | Card type<br>credit:credit card, check:debit |
| `canPartCancel` | Boolean | No | - | Whether partial cancellation is possible<br>true: Possible, false: Impossible |
| `acquCardCode` | String | No | 3 | Acquirer code |
| `acquCardName` | String | No | 100 | Acquirer Name |
| `vbankCode` | String | No | 3 | Virtual account bank code to receive deposit |
| `vbankName` | String | No | 20 | Virtual account bank name to receive deposit |
| `vbankNumber` | String | No | 20 | Virtual account number to receive deposit |
| `vbankExpDate` | String | No | - | Virtual Account Expiration Date<br><br>ISO 8601 |
| `vbankHolder` | String | No | 40 | Account holder name for issued virtual account|
| `bankCode` | String | No | 3 | Bank code |
| `bankName` | String | No | 20 | Bank name |
| `messageSource` | String | Yes |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|

The `vbankCode`, `vbankName`, `vbankNumber`, `vbankExpDate`, `vbankHolder`, `bankCode` and `bankName` fields can be empty even when `payMethod` is `vbank` or `bank`. Read the virtual account from the `vbank` object, and the bank from the `bank` object, of [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id).

<br>

### Verifying the payment result

Your Merchant Server runs these checks on every `returnUrl` callback before it confirms the order. The callback passes through the customer's browser, so your Merchant Server trusts it only after these checks.

1. Read the form fields. Your web framework URL-decodes them. Use the decoded values in the steps below.
2. Find your order by `orderId`. Do not use `sessionId` or a browser cookie for this: `sessionId` is not in every callback, and the browser does not send cookies set with `SameSite=Lax` or `SameSite=Strict` with this request. If the order is already recorded as paid, keep it paid and stop here. The customer's browser can send a second callback for the same session, and that callback reports a failure (`P045`, `P047` or `P049`) even when the payment succeeded.
3. Check `resultCode`. Continue only when it is `0000`. `success` is `false` whenever `resultCode` is not `0000`, so you do not need to check both. For any other code, do not confirm the order. `P045`, `P047` and `P049` mean that the session was already processed or is still being approved: look the payment up with [Transaction Status Inquiry (with sessionId)](./nicepay-api-retrieve.md#transaction-status-inquiry-with-sessionid) to learn its result. See [API response code](../code/nicepay-code.md#api-response-code) for the other codes.
4. Check `status`. Continue only when it is one of these:
   - `paid`: the payment is approved. Continue with step 5.
   - `ready`: NicePay issued a virtual account, and the customer has not paid yet. Run steps 5 and 6, record the order as waiting for payment, and stop. Do not ship the order. Confirm it when the deposit webhook (`status` `paid`) arrives, or when Transaction Status Inquiry returns `status` `paid`. See [Virtual account Option](#virtual-account-option).
5. Verify `signature`. Build the string `tid + amount + ediDate + SecretKey`: join the values as plain text with no separator, write `amount` as whole-number digits (`1004`), use `ediDate` exactly as received, and use the Secret key of the `clientId` that created the session. Hash the UTF-8 bytes of the string with SHA-256 and write the result as 64 lowercase hexadecimal characters. If it differs from `signature`, do not confirm the order.
6. Compare `amount` with the amount of your order. NicePay rejects an approval whose amount differs from the amount of the Create checkout request, so a different `amount` means that the callback was changed on the way. Do not confirm the order.
7. Look the payment up from your Merchant Server with [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id) (`GET /v1/payments/{tid}`, available in Sandbox and Live). Verify the `signature` of its response in the same way. Check that its `orderId` and `amount` match your order and that its `status` is `paid`. The callback's `signature` covers only `tid`, `amount` and `ediDate`, so a changed `orderId` or `status` in the callback still passes step 5. The inquiry response goes directly from NicePay to your Merchant Server.
8. Confirm the order and store `tid`. You need `tid` to look the payment up or cancel it later.

Worked example for step 5, with the public Sandbox Secret key from [Test key information](../info/nicepay-info-sandbox.md#test-key-information) and the values of the Sandbox callback in [Payment result (returnUrl callback)](../info/nicepay-info-sandbox.md#payment-result-returnurl-callback):

```bash
tid       = UT0000104m00012303241646422011
amount    = 1004
ediDate   = 2023-03-24T16:46:42.484+0900
SecretKey = 13e969a77a0545799242ccc3915243d3

String to hash:
UT0000104m0001230324164642201110042023-03-24T16:46:42.484+090013e969a77a0545799242ccc3915243d3

Expected signature:
6cd4cc86f52f15c7532f95f9be162e4f7be89292836ed02fddf6bdecb7357535
```

Handle each payment once. When a webhook URL is registered for the payment method, NicePay sends the webhook of a Checkout payment as soon as it approves the payment, so the webhook can arrive before the callback. Apply the first one that passes these checks, and ignore the other if the order is already confirmed.

If your Merchant Server does not receive the callback, the payment still stands. See [Timeout Information](../info/nicepay-info-firewall-timeout.md#timeout-information) for how to look it up and cancel it if needed.

<br><br>

### Retrieve Checkout session API

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
| `sessionId` | String  |  Yes  | 256	  | Merchant unique session id, issued by merchant | 

<br><br>

### Retrieve Checkout session Response Parameter

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `sessionId` | String  | Yes | 256	  | Merchant unique session id, issued by merchant | 
| `orderId` | String | Yes | 64 | Your Unique order ID |
| `clientId` | String | Yes | 50 | Client ID issued by NICEPAY |
| `tid` | String | No | 30 | Returned when authorization is successful |
| `amount` | Int | Yes | 12 | payment amount |
| `url` | String | Yes |   | The URL to the Checkout Session. Redirect customers to this URL to take them to Checkout. |
| `status` | String | Yes | 20 | Status of the checkout session<br><br>ready: created, not paid yet<br>pending: the customer authenticated, and approval is in progress<br>paid: approved. For a virtual account, the account was issued<br>failed: authentication or approval failed<br>closed: the customer left the Hosted Payment Page<br>cancelled: cancelled<br>partialCancelled: partially cancelled<br>['ready', 'pending', 'paid', 'failed', 'closed', 'cancelled', 'partialCancelled']<br><br>A virtual account deposit does not change this value. To learn about the deposit, use the [webhook](./nicepay-api-webhook.md#delivery-of-webhook) or [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-sessionid). In Sandbox with `fakeAuth: "true"`, the session takes the `status` of the payment, so a virtual account session shows `ready` after the account is issued.<br><br>Expiration is not represented as a `status` value; check the separate `isExpire` boolean field instead. |
| `isExpire` | Boolean | Yes |  | true : Expired <br> false : Not expired |
| `expireDate` | String | Yes |  | ISO 8601 (session validity period) |
| `messageSource` | String | Yes |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|


Parameters you requested are also echoed back in the response.

<br><br>

### Expire Checkout session API

This is an API for expiring a generated session.

<br>

### Expire Checkout session Request Parameter

```bash
POST /v1/checkout/{sessionId}/expire
HTTP/1.1    
Host: api.nicepay.co.kr
Authorization: Basic <credentials> or Bearer <token>
Content-type: application/json;charset=utf-8
```

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| `sessionId` | String  |  Yes  | 256	  | Merchant unique session id, issued by merchant | 

<br><br>

### Expire Checkout session Response Parameter

| Parameter |   Type   |  Required   |  Bytes  | Description  |
|:--------------|:----:|:-----:|:-----:|:--------|
| `resultCode` | String | Yes | 4 | 0000 : success / other failure |
| `resultMsg` | String | Yes | 100 | Result message |
| `sessionId` | String  | Yes | 256	  | Merchant unique session id, issued by merchant | 
| `orderId` | String | Yes | 64 | Your Unique order ID |
| `clientId` | String | Yes | 50 | Client ID issued by NICEPAY |
| `tid` | String | No | 30 | Returned when authorization is successful |
| `amount` | Int | Yes | 12 | payment amount |
| `url` | String | Yes |   | The URL to the Checkout Session. Redirect customers to this URL to take them to Checkout. |
| `status` | String | Yes | 20 | Status of the checkout session<br><br>ready: created, not paid yet<br>pending: the customer authenticated, and approval is in progress<br>paid: approved. For a virtual account, the account was issued<br>failed: authentication or approval failed<br>closed: the customer left the Hosted Payment Page<br>cancelled: cancelled<br>partialCancelled: partially cancelled<br>['ready', 'pending', 'paid', 'failed', 'closed', 'cancelled', 'partialCancelled']<br><br>A virtual account deposit does not change this value. To learn about the deposit, use the [webhook](./nicepay-api-webhook.md#delivery-of-webhook) or [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-sessionid). In Sandbox with `fakeAuth: "true"`, the session takes the `status` of the payment, so a virtual account session shows `ready` after the account is issued.<br><br>Expiration is not represented as a `status` value; check the separate `isExpire` boolean field instead. |
| `isExpire` | Boolean | Yes |  | true : Expired <br> false : Not expired |
| `expireDate` | String | Yes |  | ISO 8601 (session validity period) |
| `messageSource` | String | Yes |  | nicepay: Response message generated by nicepay  <br> external: Response message generated by 3rd partner|

Parameters you requested are also echoed back in the response.
