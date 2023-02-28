## URI LIST
A list of URI's for provided APIs. If you need to check the interface, please click the link in the list.

| API                                                               |     Method      |               Endpoint              |   Sandbox |
|:------------------------------------------------------------------|:-------------:|:--------------------------------------|:---------:|
| [Checkout(Payment-window)](/api/nicepay-api-payment-window-url.md) |      `POST`    |                                     |     ○     |
| [Billing Create-billkey](/api/nicepay-api-billing.md)             |      `POST`     |     /v1/subscribe/regist            |     ○     |
| [Billing Authorization](/api/nicepay-api-billing.md)              |      `POST`     |     /v1/subscribe/{bid}/payments    |     ○     |
| [Billing Delete-billkey](/api/payment-subscribe.md)               |      `POST`     |     /v1/subscribe/{bid}/expire      |     ○     |
| [Access-token](/api/nicepay-api-access-token.md)                  |      `POST`     |     /v1/access-token                |     ○     |
| [Cancel](/api/nicepay-api-cancel.md)                              |      `POST`     |     /v1/payments/{tid}/cancel       |     △     |
| [Retrieve check amount](/api/nicepay-api-retrieve.md#check-amount-api-request-parameter)              |       `GET`     |     /v1/payments/{tid}              |     ○     |
| [Retrieve transaction](/api/nicepay-api-retrieve.md#retrieve-a-transaction-with-tidtransaction-id)              |       `GET`     |     /v1/payments/{tid}              |     ○     |
| [Retrieve orderId](/api/nicepay-api-retrieve.md#retrieve-a-transaction-with-orderid)                  |       `GET`     |     /v1/payments/find/{orderId}     |     ○     |
| [Retrieve card-event](/api/nicepay-api-retrieve.md#card-event-api)               |       `GET`     |     /v1/card/event                  |     ×     |
| [Retrieve card-interest-free](/api/nicepay-api-retrieve.md#interest-free-installment-information-api)       |       `GET`     |     /v1/card/event                  |     ×     |
| [Webhook Create](/api/nicepay-api-webhook.md) |      `POST`    |     /v1/webhook/regist      |     ×     |
| [Webhook Retrieve](/api/nicepay-api-webhook.md) |      `GET`    |    /v1/webhook/get     |     ×     |
| [Webhook Update](/api/nicepay-api-webhook.md) |      `POST`    |      /v1/webhook/edit        |     ×     |
| [Webhook Delete](/api/nicepay-api-webhook.md) |      `POST`    |      /v1/webhook/remove      |     ×     |