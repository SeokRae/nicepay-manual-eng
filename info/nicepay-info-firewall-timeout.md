## Firewall Policy
Please check if the server's HTTP client supports `TLS 1.2` for safety.  
Allow the IP below in your firewall to call the `NicePay` API on the server.  

| Service              | Domain                    | IP Address                      | Direction       |
|--------------------|---------------------------|------------------------------------|----------|
| payment window (live)   | pay.nicepay.co.kr         | 121.133.126.64/27                  | OUTBOUND |
| payment window (sandbox)  | sandbox-pay.nicepay.co.kr | 121.133.126.64/27                  | OUTBOUND |
| API (live)  | api.nicepay.co.kr         | 121.133.126.64/27                  | OUTBOUND |
| API (sandbox) | sandbox-api.nicepay.co.kr | 121.133.126.64/27                  | OUTBOUND |
| Webhook  | -  | 121.133.126.86 <br> 121.133.126.87 | INBOUND  |

<br>

## Timeout Information

Information for handling timeout exceptions when configuring HTTP clients.  

- Connection timeout : `5s`
- Receive(Read) timeout : `30s`

A timeout does not tell your server whether NicePay processed the request. NicePay does not cancel a payment because your server timed out. It sends a net cancel on its own only when its own processing fails during an authorization, and then returns [`U504`](../code/nicepay-code.md#api-response-code) (Checkout, Key-in) or [`U503`](../code/nicepay-code.md#api-response-code) (Recurring). Your Merchant Server looks the payment up and then keeps it or cancels it. Follow the row for your integration.

| Integration | Result your server did not receive | Look up the payment with | Send the request again? | Cancel an approved payment with |
|:---|:---|:---|:---|:---|
| Checkout | The `returnUrl` callback after the customer pays on the Hosted Payment Page | `sessionId`, with [Transaction Status Inquiry (with sessionId)](../api/nicepay-api-retrieve.md#transaction-status-inquiry-with-sessionid), or `orderId`. [`U111`](../code/nicepay-code.md#api-response-code) from the `sessionId` lookup means the session has no payment yet. | Create checkout does not charge the customer. If that call times out, create a new session with a new `sessionId` and a new `orderId`. | The `sessionId` or the `tid`. See [Cancel](../api/nicepay-api-cancel.md#cancel-request-parameter-with-sessionid). You can send it as a net cancel (`isNetCancel: true`). |
| Key-in | The response to `POST /v1/key-in/payments` | `orderId`, with [Transaction Status Inquiry (with orderId)](../api/nicepay-api-retrieve.md#transaction-status-inquiry-with-orderid). Your server has no `tid` yet. | Never with a new `orderId`. If the lookup returns [`U107`](../code/nicepay-code.md#api-response-code), send the request again with the same `orderId`. If NicePay is still processing the first request or already approved it, NicePay rejects the new one with [`U112`](../code/nicepay-code.md#api-response-code): look the payment up again. | The `tid` from the lookup, as an ordinary cancellation with `isNetCancel` left out. See [Cancel](../api/nicepay-api-cancel.md#cancel-request-parameter-with-tid). |
| Recurring | The response to `POST /v1/subscribe/{bid}/payments` | Same as Key-in. | Same as Key-in. | Same as Key-in. |

If a cancel request times out, look the payment up and check `balanceAmt` and `cancels` before you send the cancel again. A partial cancellation sent again with the same `orderId` fails with [`U112`](../code/nicepay-code.md#api-response-code), even when the first attempt failed, so send a new attempt with a new `orderId`.
