## URI LIST
All endpoints for this API. Click a link in the table below for that endpoint's parameter reference.

| API                                                               |     Method      |               Endpoint              |   Sandbox |
|:------------------------------------------------------------------|:-------------:|:--------------------------------------|:---------:|
| [Create checkout session](./nicepay-api-payment-window-url.md#hosted-payment-page-request-parameter) |      `POST`    | /v1/checkout |     ○     |
| [Retrieve checkout session](./nicepay-api-payment-window-url.md#retrieve-checkout-session-api) |      `GET`    | /v1/checkout/{sessionId} |     ○     |
| [Expire checkout session](./nicepay-api-payment-window-url.md#expire-checkout-session-api) |      `POST`    | /v1/checkout/{sessionId}/expire |     ○     |
| [Key-in Payment](./nicepay-api-keyin.md)             |      `POST`     |     /v1/key-in/payments            |     ×     |
| [Recurring payment: Token Issue](./nicepay-api-billing.md)             |      `POST`     |     /v1/subscribe/regist            |     ○     |
| [Recurring Payment: Token authorization](./nicepay-api-billing.md)              |      `POST`     |     /v1/subscribe/{bid}/payments    |     ○     |
| [Recurring Payment: Token delete](./nicepay-api-billing.md#delete-token) |      `POST`     |     /v1/subscribe/{bid}/expire      |     ○     |
| [Recurring Payment: Bid status inquiry](./nicepay-api-billing.md#bid-status-inquiry) |      `POST`     |     /v1/subscribe/{bid}/status      |     ○     |
| [AccessToken Generation](./nicepay-api-access-token.md)                  |      `POST`     |     /v1/access-token                |     ○     |
| [Cancel request with session id](./nicepay-api-cancel.md#cancel-request-parameter-with-sessionid)  |      `POST`     |     /v1/payments/checkout/{sessionId}/cancel | full cancel only |
| [Cancel request with tid](./nicepay-api-cancel.md#cancel-request-parameter-with-tid)  |      `POST`     |     /v1/payments/{tid}/cancel       | full cancel only |
| [Transaction Status Inquiry-Authorization amount](./nicepay-api-retrieve.md#check-authorization-amount-request-parameter)              |       `GET`     |     /v1/check-amount/{tid}  |     ○     |
| [Transaction Status Inquiry-Transaction status](./nicepay-api-retrieve.md#transaction-status-inquiry-with-tidtransaction-id)  |       `GET`     |     /v1/payments/{tid}  |     ○     |
| [Transaction Status Inquiry-orderId](./nicepay-api-retrieve.md#transaction-status-inquiry-with-orderid)                  |       `GET`     |     /v1/payments/find/{orderId}     |     ○     |
| [Transaction Status Inquiry-sessionId](./nicepay-api-retrieve.md#transaction-status-inquiry-with-sessionid)   |  `GET`     |     /v1/payments/checkout/{sessionId}     |     ○     |
| [Card event inquiry](./nicepay-api-retrieve.md#card-event-api)               |       `GET`     |     /v1/card/event                  |     dummy data     |
| [Card installment inquiry](./nicepay-api-retrieve.md#interest-free-installment-information-api)       |       `GET`     |     /v1/card/interest-free                  |     dummy data     |
| [Webhook creation](./nicepay-api-webhook.md) |      `POST`    |     /v1/webhook      |     ×     |
| [Webhook Inquiry](./nicepay-api-webhook.md) |      `GET`    |    /v1/webhook     |     ×     |
| [Webhook delete](./nicepay-api-webhook.md) |      `POST`    |      /v1/webhook/{method}/delete      |     ×     |
| [Webhook update](./nicepay-api-webhook.md) |      `POST`    |      /v1/webhook/{method}/update      |     ×     |
| [Transactions](./nicepay-api-reconciliation.md) |      `GET`    |      /v1/transactions      |     ×     |
| [Settlement](./nicepay-api-reconciliation.md) |      `GET`    |      /v1/settlements     |     ×     |
