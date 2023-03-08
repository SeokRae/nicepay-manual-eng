## URI LIST
A list of URI's for provided APIs. If you need to check the interface, please click the link in the list.

| API                                                               |     Method      |               Endpoint              |   Sandbox |
|:------------------------------------------------------------------|:-------------:|:--------------------------------------|:---------:|
| [Payment Request (Hosted Payment Page)](/api/nicepay-api-payment-window-url.md) |      `POST`    |                                     |     △     |
| [Recurring payment: Token Issue](/api/nicepay-api-billing.md)             |      `POST`     |     /v1/subscribe/regist            |     ○     |
| [Recurring Payment: Token authorization](/api/nicepay-api-billing.md)              |      `POST`     |     /v1/subscribe/{bid}/payments    |     ○     |
| [Recurring Payment: Token delete](/api/payment-subscribe.md)               |      `POST`     |     /v1/subscribe/{bid}/expire      |     ○     |
| [AccessToken Generation](/api/nicepay-api-access-token.md)                  |      `POST`     |     /v1/access-token                |     ○     |
| [Cancel request](/api/nicepay-api-cancel.md)                              |      `POST`     |     /v1/payments/{tid}/cancel       |     △     |
| [Transaction Status Inquiry-Authorization amount](/api/nicepay-api-retrieve.md#check-amount-api-request-parameter)              |       `GET`     |     /v1/payments/{tid}              |     ○     |
| [Transaction Status Inquiry-Transaction status](/api/nicepay-api-retrieve.md#retrieve-a-transaction-with-tidtransaction-id)              |       `GET`     |     /v1/payments/{tid}              |     ○     |
| [Transaction Status Inquiry-orderId](/api/nicepay-api-retrieve.md#retrieve-a-transaction-with-orderid)                  |       `GET`     |     /v1/payments/find/{orderId}     |     ○     |
| [Card event inquiry](/api/nicepay-api-retrieve.md#card-event-api)               |       `GET`     |     /v1/card/event                  |     ×     |
| [Card installment inquiry](/api/nicepay-api-retrieve.md#interest-free-installment-information-api)       |       `GET`     |     /v1/card/event                  |     ×     |
| [Webhook creation](/api/nicepay-api-webhook.md) |      `POST`    |     /v1/webhook/regist      |     ×     |
| [Webhook Inquiry](/api/nicepay-api-webhook.md) |      `GET`    |    /v1/webhook/get     |     ×     |
| [Webhook update](/api/nicepay-api-webhook.md) |      `POST`    |      /v1/webhook/remove      |     ×     |
| [Transactions](/api/nicepay-api-reconciliation.md) |      `POST`    |      /v1/transactions      |     ×     |
| [Settlement](/api/nicepay-api-reconciliation.md) |      `POST`    |      /v1/settlements     |     ×     |
