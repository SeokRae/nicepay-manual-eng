# Firewall and Timeout

## Firewall Policy
These rules are for the firewall in front of your Merchant Server. Your server's HTTP client must support `TLS 1.2` or later.

| Traffic | Direction | Domain | IP address | Port |
|:--------|:---------:|:-------|:-----------|:----:|
| Your server calls the API (Live) | Outbound | `api.nicepay.co.kr` | `121.133.126.64/27` | `443` |
| Your server calls the API (Sandbox) | Outbound | `sandbox-api.nicepay.co.kr` | `121.133.126.64/27` | `443` |
| NicePay sends [webhooks](../api/nicepay-api-webhook.md) to your server | Inbound | None | `121.133.126.86`, `121.133.126.87` | The port of your webhook URL |

The customer's browser, not your server, opens the Hosted Payment Page at `pay.nicepay.co.kr` (Live) or `sandbox-pay.nicepay.co.kr` (Sandbox), on port `443`. Your server's firewall needs no rule for it. If your office network filters the browsers of your staff or test devices, allow those two domains there.

<br>

## Timeout Information

Information for handling timeout exceptions when configuring HTTP clients.  

- Connection timeout : `5s`
- Receive(Read) timeout : `30s`

A timeout does not tell your server whether NicePay processed the request. NicePay does not cancel a payment because your server timed out. It sends a net cancel on its own only when its own processing fails during an authorization, and then returns [`U504`](../code/nicepay-code.md#code-u504) (Checkout, Key-in) or [`U503`](../code/nicepay-code.md#code-u503) (Recurring). Your Merchant Server looks the payment up and then keeps it or cancels it. Within 1 hour after the payment, it can cancel with a [Net cancel](../api/nicepay-api-cancel.md#net-cancel) by `orderId`, which does not need the `tid`. Follow the row for your integration.

| Integration | Result your server did not receive | Look up the payment with | Send the request again? | Cancel an approved payment with |
|:---|:---|:---|:---|:---|
| Checkout | The `returnUrl` callback after the customer pays on the Hosted Payment Page | `sessionId`, with [Transaction Status Inquiry (with sessionId)](../api/nicepay-api-retrieve.md#transaction-status-inquiry-with-sessionid), or `orderId`. [`U111`](../code/nicepay-code.md#code-u111) from the `sessionId` lookup means the session has no payment yet. | Create checkout does not charge the customer. If that call times out, create a new session with a new `sessionId` and a new `orderId`. | The `sessionId` or the `tid`, see [Cancel](../api/nicepay-api-cancel.md#cancel-request-parameter-with-sessionid). Within 1 hour after the payment, the `orderId` with [Net cancel](../api/nicepay-api-cancel.md#net-cancel). |
| Key-in | The response to `POST /v1/key-in/payments` | `orderId`, with [Transaction Status Inquiry (with orderId)](../api/nicepay-api-retrieve.md#transaction-status-inquiry-with-orderid). Your server has no `tid` yet. | Never with a new `orderId`. If the lookup returns [`U107`](../code/nicepay-code.md#code-u107), send the request again with the same `orderId`. If NicePay is still processing the first request or already approved it, NicePay rejects the new one with [`U112`](../code/nicepay-code.md#code-u112): look the payment up again. | The `tid` from the lookup, see [Cancel](../api/nicepay-api-cancel.md#cancel-request-parameter-with-tid). Within 1 hour after the payment, the `orderId` with [Net cancel](../api/nicepay-api-cancel.md#net-cancel). |
| Recurring | The response to `POST /v1/subscribe/{bid}/payments` | Same as Key-in. | Same as Key-in. | Same as Key-in. |

If a cancel request times out, look the payment up and check `balanceAmt` and `cancels` before you send the cancel again. A partial cancellation sent again with the same `orderId` fails with [`U112`](../code/nicepay-code.md#code-u112), even when the first attempt failed, so send a new attempt with a new `orderId`.
