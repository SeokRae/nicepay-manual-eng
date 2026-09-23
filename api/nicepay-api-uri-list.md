## URI LIST
All endpoints for this API. Click a link in the table below for that endpoint's parameter reference.

| API                                                               |     Method      |               Endpoint              |   Sandbox |
|:------------------------------------------------------------------|:-------------:|:--------------------------------------|:---------:|
| [Create checkout session](./nicepay-api-payment-window-url.md#hosted-payment-page-request-parameter) |      `POST`    | `/v1/checkout` |     Yes     |
| [Retrieve checkout session](./nicepay-api-payment-window-url.md#retrieve-checkout-session-api) |      `GET`    | `/v1/checkout/{sessionId}` |     Yes     |
| [Expire checkout session](./nicepay-api-payment-window-url.md#expire-checkout-session-api) |      `POST`    | `/v1/checkout/{sessionId}/expire` |     Yes     |
| [Key-in Payment](./nicepay-api-keyin.md)             |      `POST`     |     `/v1/key-in/payments`            |     No     |
| [Recurring payment: Token Issue](./nicepay-api-billing.md)             |      `POST`     |     `/v1/subscribe/regist`            |     Yes     |
| [Recurring Payment: Token authorization](./nicepay-api-billing.md)              |      `POST`     |     `/v1/subscribe/{bid}/payments`    |     Yes     |
| [Recurring Payment: Token delete](./nicepay-api-billing.md#delete-token) |      `POST`     |     `/v1/subscribe/{bid}/expire`      |     Yes     |
| [Recurring Payment: Bid status inquiry](./nicepay-api-billing.md#bid-status-inquiry) |      `POST`     |     `/v1/subscribe/{bid}/status`      |     Yes     |
| [AccessToken Generation](./nicepay-api-access-token.md)                  |      `POST`     |     `/v1/access-token`                |     Yes     |
| [Cancel request with session id](./nicepay-api-cancel.md#cancel-request-parameter-with-sessionid)  |      `POST`     |     `/v1/payments/checkout/{sessionId}/cancel` | Full cancel only |
| [Cancel request with tid](./nicepay-api-cancel.md#cancel-request-parameter-with-tid)  |      `POST`     |     `/v1/payments/{tid}/cancel`       | Full cancel only |
| [Transaction Status Inquiry-Authorization amount](./nicepay-api-retrieve.md#check-authorization-amount-request-parameter)              |       `GET`     |     `/v1/check-amount/{tid}`  |     Yes     |
| [Transaction Status Inquiry-Transaction status](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id)  |       `GET`     |     `/v1/payments/{tid}`  |     Yes     |
| [Transaction Status Inquiry-orderId](./nicepay-api-retrieve.md#transaction-status-inquiry-with-orderid)                  |       `GET`     |     `/v1/payments/find/{orderId}`     |     Yes     |
| [Transaction Status Inquiry-sessionId](./nicepay-api-retrieve.md#transaction-status-inquiry-with-sessionid)   |  `GET`     |     `/v1/payments/checkout/{sessionId}`     |     Yes     |
| [Card event inquiry](./nicepay-api-retrieve.md#card-event-api)               |       `GET`     |     `/v1/card/event`                  |     Dummy data     |
| [Card installment inquiry](./nicepay-api-retrieve.md#interest-free-installment-information-api)       |       `GET`     |     `/v1/card/interest-free`                  |     Dummy data     |
| [Webhook creation](./nicepay-api-webhook.md) |      `POST`    |     `/v1/webhook`      |     No     |
| [Webhook Inquiry](./nicepay-api-webhook.md) |      `GET`    |    `/v1/webhook`     |     No     |
| [Webhook delete](./nicepay-api-webhook.md) |      `POST`    |      `/v1/webhook/{method}/delete`      |     No     |
| [Webhook update](./nicepay-api-webhook.md) |      `POST`    |      `/v1/webhook/{method}/update`      |     No     |
| [Transactions](./nicepay-api-reconciliation.md) |      `GET`    |      `/v1/transactions`      |     No     |
| [Settlement](./nicepay-api-reconciliation.md) |      `GET`    |      `/v1/settlements`     |     No     |
