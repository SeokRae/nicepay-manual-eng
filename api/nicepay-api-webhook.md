## Webhook

You can use Webhook to implement additional business logic by receiving API events as server-side responses.

- If you use a payment method such as virtual account that causes a time difference between virtual account creation and deposit time, webhook implementation is absolutely necessary.

> **⚠️ Important:** Webhook registration/inquiry/delete/update is not available in [Sandbox](../info/nicepay-info-sandbox.md); test against Live once your integration is ready.  
> When you register or update a webhook URL, NicePay first sends a test request to that URL, and your endpoint must answer it within 5 seconds. The request fails with [`U336`](../code/nicepay-code.md#api-response-code) if NicePay cannot reach the URL or gets no answer in time, `U337` if the response status is not `200`, and `U338` if the response body is not `OK`. The test request is not a real payment: answer it with HTTP `200` and `OK`, and do not apply it to an order.  
> To test payment and cancellation events, make a small Live payment with a payment method that has a registered webhook URL, then cancel it. NicePay sends a webhook for the payment and another for the cancellation.  

<br>

### Over-view
<img alt="Sequence diagram of the webhook event delivery flow: NicePay pushes a payment or status-change event to the merchant server's registered webhook endpoint, the merchant server checks the signature and amount and processes the event, then responds with HTTP 200 and an OK body to acknowledge receipt; if delivery fails (network error, non-200 response, or a body other than OK), NicePay retries delivery on a configured schedule until it receives an OK acknowledgement or reaches the retry limit" src="../image/payment-webhook.svg" width="800px">

<br>

### Webhook dispatch flow
- When an event occurs, NicePay sends an HTTP `POST` with a JSON body to the URL registered for the payment method of the payment. The request comes from the webhook IP addresses in [Firewall Policy](../info/nicepay-info-firewall-timeout.md#firewall-policy): allow them in your firewall. The request has no authentication header, so verify each event with its `signature`, see [Verifying a webhook](#verifying-a-webhook).
- Respond with HTTP status `200` and a response body of exactly `OK`. NicePay ignores case and leading or trailing whitespace, so `ok` also works. Any other status (including `201` and `204`), an empty body, or a longer body such as `{"result":"OK"}` counts as a failed delivery. Register the final URL of your endpoint, not one that redirects.
- NicePay waits up to 5 seconds to connect and up to 15 seconds for your response. A slower response counts as a failed delivery. Store the event and respond with `OK` first, then run your business logic.
- After a failed delivery, NicePay sends the event again on a one-minute schedule. For a URL registered with [Create a webhook](#create-a-webhook-), NicePay makes up to 10 attempts per event, including the first. After the last failed attempt, NicePay emails your account's registered admin address and stops sending that event.
- The same event can arrive more than once, even after your endpoint responded with `OK`. A resent event has the same body, including `ediDate` and `signature`, although the order of the keys can differ. One payment also produces several events with the same `tid`, see [Delivery of webhook](#delivery-of-webhook). Store each event once under the key `tid` + `status` + `cancelledTid`, not under `tid` alone.

<br>

### Delivery of webhook
NicePay sends a webhook for each event below. The body is the same JSON as the API response for that payment, see [Webhook event payload (sent by NicePay)](#webhook-event-payload-sent-by-nicepay).

| Event | `status` | `payMethod` | How to recognize it |
|:---|:---|:---|:---|
| Payment approved (card, easy pay, bank transfer, mobile phone) | `paid` | `card`, `naverpay`, `kakaopay`, `payco`, `ssgpay`, `samsungpay`, `tosspay`, `bank` or `cellphone` | `cancelledTid` is `null` |
| Virtual account issued | `ready` | `vbank` | The `vbank` object holds the account. The customer has not paid yet |
| Virtual account deposit | `paid` | `vbank` | The customer paid into the account. The body is the [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id) response for the `tid` |
| Cancellation, full or partial | `cancelled` or `partialCancelled` | Same as the payment | `cancelledTid` holds the `tid` of the latest cancellation, and `cancels` lists every cancellation |

A cancellation event can also arrive for a payment that you never saw as approved. When NicePay's own processing fails after an approval, NicePay cancels the payment (net cancel) and sends a cancellation event.

Each event goes to the URL registered for the payment method:

- `card`: card and easy pay payments, Key-in payments, Recurring Payment charges, and their cancellations
- `bank`: bank transfer payments and their cancellations
- `vbank`: virtual account issued, deposit and cancellation events
- `cellphone`: mobile phone payments and their cancellations

A URL registered with `all` receives all of them.

By integration:

- **Checkout**: NicePay sends the payment event, or the virtual account issued event, as soon as it approves the payment. The event can arrive before the `returnUrl` callback.
- **Key-in**: NicePay sends a payment event after an approved Key-in payment. Its body has the fields of [Key-in Payment Response Parameter](./nicepay-api-keyin.md#key-in-payment-response-parameter): `status` is `paid`, and there is no `payMethod` or `cancelledTid`.
- **Recurring Payment**: NicePay sends a payment event after an approved charge with `/v1/subscribe/{bid}/payments`. Its body also has `bid`. Registering or deleting a token sends no webhook.

NicePay sends every event other than the Checkout events above from a job that runs every minute. This includes cancellations and virtual account deposits.

NicePay sends no webhook for a declined or failed payment, so every webhook has `resultCode` `0000`. It also sends none for a payment method that has no registered URL at the time of the event. Use [Transaction Status Inquiry](./nicepay-api-retrieve.md) for those results.

<br>

### Verifying a webhook

Verify every event before you apply it to an order. The request has no authentication header, so the `signature` in the body is how you know that the event came from NicePay.

1. Build the string `tid + amount + ediDate + SecretKey` from the top-level `tid`, `amount` and `ediDate` of the body and the Secret key of the `clientId` whose request caused the event (the payment or the cancellation). Join the values as plain text with no separator, write `amount` as whole-number digits (`1004`), and use `ediDate` exactly as received.
2. Hash the UTF-8 bytes of the string with SHA-256, write the result as 64 lowercase hexadecimal characters, and compare it with `signature`.
3. If `signature` is missing or does not match, do not apply the event. Look the payment up with [Transaction Status Inquiry](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id) (`GET /v1/payments/{tid}`), verify the `signature` of that response in the same way, and use its `status` and `amount`. Still answer the webhook with HTTP `200` and `OK`, so that NicePay does not send it again.
4. The `signature` covers only `tid`, `amount` and `ediDate`, not `status` or `orderId`. The virtual account issued event and the deposit event have the same `tid` and `amount`. Before you ship an order because of a deposit event (`payMethod` `vbank`, `status` `paid`), confirm `status` with Transaction Status Inquiry.
5. Match the event to your order. For a payment, virtual account issued or deposit event, find the order by `orderId` and compare `amount` with the order amount. For a cancellation event, find the order by `tid`: `amount` is still the amount of the original payment, `balanceAmt` is the amount left after all cancellations, and `cancels` lists each cancelled amount.

If your merchant account has more than one Client and Secret key pair, the deposit event of a virtual account can be signed with the Secret key of another pair. Try each of your Secret keys before you treat that event as unverified.

The test request that NicePay sends when you register or update a URL is not a real payment, and it can fail verification. Answer it with HTTP `200` and `OK`, and do not apply it to an order.

Worked example with an example Secret key (not a real key). These are the values of the example body in [Webhook event payload (sent by NicePay)](#webhook-event-payload-sent-by-nicepay):

```bash
tid       = nicuntct1m0101210727200125A056
amount    = 1004
ediDate   = 2021-07-27T20:01:25.000+0900
SecretKey = 0123456789abcdef0123456789abcdef

String to hash:
nicuntct1m0101210727200125A05610042021-07-27T20:01:25.000+09000123456789abcdef0123456789abcdef

Expected signature:
61ce750d1cda992ae8406f5269d09a3e27e0cb8ec9902da6008c9f02cf37c723
```

<br>

### Webhook receiver example code

The examples below verify the event, store it once under the key `tid` + `status` + `cancelledTid`, and answer `OK`. Your business logic (finding the order, comparing `amount`, shipping) runs later from the stored events. A slow step or a business result, such as an unknown order or a failed check, therefore never makes NicePay send the event again. Only a failed write to your store returns an error, and then NicePay sends the event again.

Both examples keep the events in memory so that they run as written: replace that store with your database. The Python example decodes the body with the charset of the `Content-Type` header, see [Webhook event payload (sent by NicePay)](#webhook-event-payload-sent-by-nicepay).

```java
// Java, Spring example
import java.nio.charset.StandardCharsets;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentMap;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class NicePayWebhookController {

    // Secret key of the clientId whose request caused the event. Load it from your configuration.
    private static final String SECRET_KEY = "your-secret-key";

    // Replace this map with a database table that keeps one row per event key.
    // If the database write fails, let the exception return an error so that NicePay sends the event again.
    private final ConcurrentMap<String, Map<String, Object>> events = new ConcurrentHashMap<>();

    @PostMapping("/hook")
    public ResponseEntity<String> hook(@RequestBody Map<String, Object> event) throws NoSuchAlgorithmException {
        String tid = String.valueOf(event.get("tid"));
        String amount = String.valueOf(event.get("amount"));
        String ediDate = String.valueOf(event.get("ediDate"));
        String signature = String.valueOf(event.get("signature"));

        String expected = sha256Hex(tid + amount + ediDate + SECRET_KEY);
        boolean verified = MessageDigest.isEqual(
                expected.getBytes(StandardCharsets.UTF_8), signature.getBytes(StandardCharsets.UTF_8));

        // One tid can have several events (for example ready then paid, or a payment then cancellations).
        // A resent event has the same key, so it is stored only once.
        String eventKey = tid + ":" + event.get("status") + ":" + event.get("cancelledTid");
        Map<String, Object> stored = new HashMap<>(event);
        stored.put("signatureVerified", verified);
        events.putIfAbsent(eventKey, stored);

        // Apply stored events to your orders in a separate job: match the order and compare amount there.
        // An unknown order or a failed check is a business outcome, not a delivery failure, so answer OK.
        return ResponseEntity.ok("OK");
    }

    private static String sha256Hex(String text) throws NoSuchAlgorithmException {
        byte[] hash = MessageDigest.getInstance("SHA-256").digest(text.getBytes(StandardCharsets.UTF_8));
        StringBuilder hex = new StringBuilder();
        for (byte b : hash) {
            hex.append(String.format("%02x", b));
        }
        return hex.toString();
    }
}
```

<br>

```python
# Python, Flask example
import hashlib
import hmac
import json

from flask import Flask, request

app = Flask(__name__)

# Secret key of the clientId whose request caused the event. Load it from your configuration.
SECRET_KEY = "your-secret-key"

# Replace this dict with a database table that keeps one row per event key.
# If the database write fails, let the exception return an error so that NicePay sends the event again.
events = {}


@app.route("/hook", methods=["POST"])
def hook():
    charset = request.mimetype_params.get("charset", "utf-8")
    event = json.loads(request.get_data().decode(charset))

    tid = str(event.get("tid"))
    signed = tid + str(event.get("amount")) + str(event.get("ediDate")) + SECRET_KEY
    expected = hashlib.sha256(signed.encode("utf-8")).hexdigest()
    verified = hmac.compare_digest(expected, str(event.get("signature")))

    # One tid can have several events (for example ready then paid, or a payment then cancellations).
    # A resent event has the same key, so it is stored only once.
    event_key = f"{tid}:{event.get('status')}:{event.get('cancelledTid')}"
    events.setdefault(event_key, dict(event, signatureVerified=verified))

    # Apply stored events to your orders in a separate job: match the order and compare amount there.
    # An unknown order or a failed check is a business outcome, not a delivery failure, so answer OK.
    return "OK", 200
```

<br><br>

### Webhook event payload (sent by NicePay)

```bash
POST
Content-type: application/json;charset=UTF-8
```

The charset is the `returnCharSet` of the request that caused the event, in upper case, and `UTF-8` when that request sent none. The payment event of a Checkout payment and the virtual account deposit event are always `UTF-8`.

Example: a card payment made through Checkout. It is signed with the example Secret key of [Verifying a webhook](#verifying-a-webhook). Do not depend on the order of the keys: a resent event can list them in a different order.

```bash
{
  "resultCode": "0000",
  "resultMsg": "정상 처리되었습니다.",
  "tid": "nicuntct1m0101210727200125A056",
  "cancelledTid": null,
  "orderId": "order-id-unique-order-001",
  "ediDate": "2021-07-27T20:01:25.000+0900",
  "signature": "61ce750d1cda992ae8406f5269d09a3e27e0cb8ec9902da6008c9f02cf37c723",
  "status": "paid",
  "paidAt": "2021-07-27T20:01:25.000+0900",
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
  "approveNo": "12345678",
  "buyerName": null,
  "buyerTel": null,
  "buyerEmail": null,
  "receiptUrl": "https://npg.nicepay.co.kr/issue/IssueLoader.do?type=0&innerWin=Y&TID=nicuntct1m0101210727200125A056",
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
  "sessionId": "unique-sessionId-001",
  "messageSource": "nicepay"
}
```

A webhook for a Key-in payment has the fields of [Key-in Payment Response Parameter](./nicepay-api-keyin.md#key-in-payment-response-parameter) instead of the table below.

Dates and times that NicePay returns are in Korea Standard Time (KST, UTC+9), for example `2023-03-24T14:04:16.982+0900`. See [Dates in responses](../info/nicepay-info-general.md#dates-in-responses) for how to parse them.

Required: Yes = has a non-empty value in every response whose `resultCode` is `0000`; No = can be `null`, empty, or left out. For a field of an object or array, Yes applies whenever that object or array element is present.

| Parameter | Type | Required | Bytes | Description |
|:------------------|:--------:|:-----:|:------:|:---------------------------------------------------------------------------------------------------------------------|
| `resultCode` | String | Yes | 4 | Always `0000` for a webhook |
| `resultMsg` | String | Yes | 100 | Result message |
| `sessionId` | String | No | 256 | Checkout session ID of the payment<br>Sent only when the payment was made through Checkout; otherwise the key is left out. A webhook for a cancellation made with `tid` does not include it |
| `bid` | String | No | 30 | Token of a Recurring Payment charge<br>Sent only for a charge made with `/v1/subscribe/{bid}/payments`; otherwise the key is left out |
| `tid` | String | Yes | 30 | NICEPAY transaction ID |
| `cancelledTid` | String | No | 30 | Cancellation transaction ID<br>- Responded only with cancellation requests<br>- Use when finding canceled transaction information in the cancels object. |
| `orderId` | String | Yes | 64 | Unique order number |
| `ediDate` | String | Yes | - | Response message creation date and time (ISO 8601 format) |
| `signature` | String | Yes | 256 | Forgery verification data<br>Rule: hex(sha256(tid + amount + ediDate + SecretKey)), see [Verifying a webhook](#verifying-a-webhook)<br>Covers only `tid`, `amount` and `ediDate`. Check `status` separately |
| `status` | String | Yes | 20 | Payment processing status<br>paid: payment approved, or virtual account deposit received<br>ready: virtual account issued, not paid yet<br>cancelled: cancelled<br>partialCancelled: partially cancelled<br>['paid', 'ready', 'cancelled', 'partialCancelled']<br>No webhook is sent for a failed payment |
| `paidAt` | String | Yes | - | Time of payment completed ISO 8601 format<br>If payment is not completed, return 0<br>For a virtual account that is not paid yet, the time the account number was requested |
| `failedAt` | String | Yes | - | Time of payment failure ISO 8601 format<br>If not payment is not failed, return 0 |
| `cancelledAt` | String | Yes | - | Payment cancellation time ISO 8601 format<br>If it is not cancellation request, return 0<br>In case of partial cancellation, the last cancellation time will be return |
| `payMethod` | String | Yes | 10 | Payment method<br><br>card: credit card, <br>vbank: virtual account, <br>bank: account transfer, <br>cellphone: mobile phone, <br>naverpay=Naver Pay, <br>kakaopay=Kakao Pay, <br>samsungpay=Samsung Pay, <br>payco=Payco, <br>ssgpay=SSG Pay, <br>tosspay=Toss Pay |
| `amount` | Int | Yes | 12 | Amount of the original payment, also in a cancellation event |
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
|            | `bankName`  |  String  | Yes |   20   | Bank name |

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
|    `method`     | String  |  Yes  | 20	  |  all : all payment methods <br> card : credit and debit cards <br> bank : bank transfer <br> vbank : virtual account  <br> cellphone : carrier billing | 
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
|           | `method` | String | Yes | 20 | Payment method of this URL<br>card : credit and debit cards <br> bank : bank transfer <br> vbank : virtual account <br> cellphone : carrier billing<br>A URL registered with `all` is returned as four elements, one per method |
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
|           | `method` | String | Yes | 20 | Payment method of this URL<br>card : credit and debit cards <br> bank : bank transfer <br> vbank : virtual account <br> cellphone : carrier billing<br>A URL registered with `all` is returned as four elements, one per method |
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
|    `method`     | String  |  Yes  | 20	  |  all : all payment methods <br> card : credit and debit cards <br> bank : bank transfer <br> vbank : virtual account  <br> cellphone : carrier billing | 

<br>

### Delete webhook Response Parameter

| Parameter | Field | Type   |  Required   |  Bytes  | Description  |
|:----------|:----:|:------:|:-----------:|:-------:|:-------------|
| `resultCode`|      | String | Yes         | 4       | 0000 : success / other failure |
| `resultMsg` |      | String | Yes         | 100     | Result message |
| `urls` | | Array | No | | All webhook URLs registered for your account after this request, one element per payment method, in no fixed order<br>An empty array after you delete the last URL |
|           | `method` | String | Yes | 20 | Payment method of this URL<br>card : credit and debit cards <br> bank : bank transfer <br> vbank : virtual account <br> cellphone : carrier billing<br>A URL registered with `all` is returned as four elements, one per method |
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
|    `method`     | String  |  Yes  | 20	  |  all : all payment methods <br> card : credit and debit cards <br> bank : bank transfer <br> vbank : virtual account  <br> cellphone : carrier billing | 
| `url` | String | Yes | 200 | The URL of the webhook endpoint |
| `managerEmail` | String | No | 255 |Stored and echoed back on lookup, but NOT the address NicePay emails on delivery failure; that notification goes to your merchant account's registered admin email instead|

<br>

### Update webhook Response Parameter

| Parameter | Field | Type   |  Required   |  Bytes  | Description  |
|:----------|:----:|:------:|:-----------:|:-------:|:-------------|
| `resultCode`|      | String | Yes         | 4       | 0000 : success / other failure |
| `resultMsg` |      | String | Yes         | 100     | Result message |
| `urls` | | Array | No | | All webhook URLs registered for your account after this request, one element per payment method, in no fixed order |
|           | `method` | String | Yes | 20 | Payment method of this URL<br>card : credit and debit cards <br> bank : bank transfer <br> vbank : virtual account <br> cellphone : carrier billing<br>A URL registered with `all` is returned as four elements, one per method |
|           | `url` | String | Yes | 200 | The URL of the webhook endpoint |
|           | `managerEmail` | String | No | 255 | Stored and echoed back on lookup, but NOT the address NicePay emails on delivery failure; that notification goes to your merchant account's registered admin email instead<br>`null` if you did not send one |
| `messageSource` | | String | Yes | | Always `nicepay` for this API |

Related error codes: `U100`, `U111`, `U133`, `U333`, `U334`, `U335`, `U336`, `U337`, `U338`, `U700`, `U701`, see [API Response code](../code/nicepay-code.md#api-response-code).
