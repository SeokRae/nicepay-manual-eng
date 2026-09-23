## Client and Secret key

<br>

### Where to get your keys

1. Sign up for a NicePay For Startups account on the [admin console](https://start.nicepay.co.kr/merchant/login/main.do) (`start.nicepay.co.kr`).
2. After you log in, create a test merchant. This is your Sandbox account.
3. Open the test merchant's `Development Information` tab. It shows the client key and the secret key.

Your Live keys are on the `Development Information` tab of your Live merchant, and they differ from your Sandbox keys. This manual does not state who can open a Live merchant account, including companies outside Korea: [open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) to ask.

To try the API before you sign up, use the public Sandbox key in [Test key information](./nicepay-info-sandbox.md#test-key-information).

<br>

### Client key
The client key is shown on the `Development Information` tab of the admin console.  
It is sent as `clientId` and is the user name part of the [Basic authentication](./nicepay-info-basic-token.md) header.  

### Client Key Type
When you issue a client key, you choose one of two approval models:

- Client Authentication: NicePay approves the payment as soon as the customer completes authentication on the Hosted Payment Page.
- Server Authentication: the Hosted Payment Page only authenticates the customer, and your Merchant Server then calls a separate approval API.

The APIs in this manual use a **Client Authentication** key. Checkout works only with a Client Authentication key: with a Server Authentication key, Create checkout still returns a session, but the Hosted Payment Page rejects the customer with [`P025`](../code/nicepay-code.md#api-response-code) (`tosspayBill` fails earlier, at Create checkout, with [`U147`](../code/nicepay-code.md#api-response-code)). The separate approval API that a Server Authentication key needs is not part of this manual. Key-in Payment and Recurring Payment accept either key type.

<br>

### Secret key
The generated secret key is used to create an API authentication key.

> **⚠️ Important:** The Sandbox and Live `secret key` values are different.  
> If you convert from Sandbox to Live, you must update to the Live `secret key`.  
> Be careful not to expose the secret key.  
