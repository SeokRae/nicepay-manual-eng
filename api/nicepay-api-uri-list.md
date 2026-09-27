## API list
All endpoints for this API. Each link in the table goes to that endpoint's request parameters.

Send Live requests to `https://api.nicepay.co.kr` and Sandbox requests to `https://sandbox-api.nicepay.co.kr`, followed by the endpoint path.

The Sandbox column is the one list of Sandbox availability in this manual. An endpoint marked Yes still answers with simulated data in Sandbox, see [Sandbox limitations](../info/nicepay-info-sandbox.md#sandbox-limitations).

| API | Method | Endpoint | Sandbox |
|:----|:------:|:---------|:-------:|
| [Create checkout](./nicepay-api-payment-window-url.md#hosted-payment-page-request-parameter) | `POST` | `/v1/checkout` | Yes |
| [Retrieve checkout session](./nicepay-api-payment-window-url.md#retrieve-checkout-session-request-parameter) | `GET` | `/v1/checkout/{sessionId}` | Yes |
| [Expire checkout session](./nicepay-api-payment-window-url.md#expire-checkout-session-request-parameter) | `POST` | `/v1/checkout/{sessionId}/expire` | Yes |
| [Key-in Payment](./nicepay-api-keyin.md#key-in-payment-request-parameter) | `POST` | `/v1/key-in/payments` | No |
| [Recurring Payment: Create Token](./nicepay-api-billing.md#create-tokenbid-request-parameter) | `POST` | `/v1/subscribe/regist` | Yes |
| [Recurring Payment: Authorization](./nicepay-api-billing.md#recurring-payment---authorization-request-parameter) | `POST` | `/v1/subscribe/{bid}/payments` | Yes |
| [Recurring Payment: Delete Token](./nicepay-api-billing.md#delete-tokenbid-request-parameter) | `POST` | `/v1/subscribe/{bid}/expire` | Yes |
| [Access token](./nicepay-api-access-token.md#access-token-api-request-parameter) | `POST` | `/v1/access-token` | Yes |
| [Cancel (with sessionId)](./nicepay-api-cancel.md#cancel-request-parameter-with-sessionid) | `POST` | `/v1/payments/checkout/{sessionId}/cancel` | Full cancel only |
| [Cancel (with tid)](./nicepay-api-cancel.md#cancel-request-parameter-with-tid) | `POST` | `/v1/payments/{tid}/cancel` | Full cancel only |
| [Net cancel](./nicepay-api-cancel.md#net-cancel-request-parameter) | `POST` | `/v1/payments/netcancel` | Yes |
| [Check Authorization Amount](./nicepay-api-retrieve.md#check-authorization-amount-request-parameter) | `POST` | `/v1/check-amount/{tid}` | Yes |
| [Transaction Status Inquiry (with tid)](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id) | `GET` | `/v1/payments/{tid}` | Yes |
| [Transaction Status Inquiry (with orderId)](./nicepay-api-retrieve.md#transaction-status-inquiry-with-orderid) | `GET` | `/v1/payments/find/{orderId}` | Yes |
| [Transaction Status Inquiry (with sessionId)](./nicepay-api-retrieve.md#transaction-status-inquiry-with-sessionid) | `GET` | `/v1/payments/checkout/{sessionId}` | Yes |
| [Card event inquiry](./nicepay-api-retrieve.md#card-event-api-request-parameter) | `GET` | `/v1/card/event` | Dummy data |
| [Interest-free installment inquiry](./nicepay-api-retrieve.md#interest-free-installment-information-request-parameter) | `GET` | `/v1/card/interest-free` | Dummy data |
| [Create a webhook](./nicepay-api-webhook.md#create-webhook-request-parameter) | `POST` | `/v1/webhook` | Yes |
| [Retrieve webhooks](./nicepay-api-webhook.md#retrieve-webhook-request-parameter) | `GET` | `/v1/webhook` | Yes |
| [Delete a webhook](./nicepay-api-webhook.md#delete-webhook-request-parameter) | `POST` | `/v1/webhook/{method}/delete` | Yes |
| [Update a webhook](./nicepay-api-webhook.md#update-webhook-request-parameter) | `POST` | `/v1/webhook/{method}/update` | Yes |
| [Transaction Search](./nicepay-api-reconciliation.md) | `GET` | `/v1/transactions` | No |
| [Settlement](./nicepay-api-reconciliation.md) | `GET` | `/v1/settlements` | No |
