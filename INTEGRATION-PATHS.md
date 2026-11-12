## Which Integration Should I Use?

NicePay For Startups offers two integration paths. They're distinguished by **who holds the card details**, and that single question decides which one fits your use case. They also differ in the payment methods they take, see the Payment methods row below.

<table>
  <thead>
    <tr>
      <th scope="col">Aspect</th>
      <th scope="col">Checkout</th>
      <th scope="col">Recurring Payment</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Card details held by</th>
      <td>The customer, entered on NicePay's Hosted Payment Page</td>
      <td>The merchant, when your server registers the token with <code>/v1/subscribe/regist</code></td>
    </tr>
    <tr>
      <th scope="row">Payment methods</th>
      <td>Cards, bank transfer, virtual accounts, mobile phone billing and Korean easy-pay wallets, chosen with <a href="./api/nicepay-api-payment-window-url.html#hosted-payment-page-request-parameter"><code>method</code></a></td>
      <td>Cards registered with <code>/v1/subscribe/regist</code></td>
    </tr>
    <tr>
      <th scope="row">Entry point</th>
      <td><code>POST /v1/checkout</code></td>
      <td><code>POST /v1/subscribe/regist</code> then <code>POST /v1/subscribe/{bid}/payments</code></td>
    </tr>
    <tr>
      <th scope="row">Flow shape</th>
      <td>Asynchronous: redirect the customer, then receive a <code>returnUrl</code> callback</td>
      <td>Synchronous: register once, charge many times</td>
    </tr>
    <tr>
      <th scope="row">Typical use case</th>
      <td>Standard online checkout, customer is present</td>
      <td>Subscriptions, memberships, installment billing</td>
    </tr>
    <tr>
      <th scope="row">Sandbox available</th>
      <td>Yes</td>
      <td>Yes</td>
    </tr>
    <tr>
      <th scope="row">PCI scope for the merchant</th>
      <td>Minimal: your server never touches raw card numbers</td>
      <td>Full for the system that calls <code>/v1/subscribe/regist</code>: your server receives and encrypts raw card data, see <a href="./info/nicepay-info-pci-dss.html">PCI-DSS Overview</a>. Each charge sends only the token (<code>bid</code>).</td>
    </tr>
    <tr>
      <th scope="row">Guide</th>
      <td><a href="./QUICKSTART.html">Quick Start Guide</a></td>
      <td><a href="./api/nicepay-api-billing.html">Recurring Payment</a></td>
    </tr>
  </tbody>
</table>

See [API list](./api/nicepay-api-uri-list.md) for the Sandbox availability of every endpoint, and [Sandbox limitations](./info/nicepay-info-sandbox.md#sandbox-limitations) for what differs from Live.

<br>

### Still not sure?

- **Building a normal storefront where the customer checks out themselves?** Use **Checkout**. It's the default path this guide's [Quick Start Guide](./QUICKSTART.md) walks through, and it keeps raw card data off your server entirely.
- **Billing the same customer repeatedly (subscription, membership)?** Use **Recurring Payment** to register a token once, then call [Recurring Payment - Authorization](./api/nicepay-api-billing.md#recurring-payment---authorization) yourself each time a charge is due (there's no automatic billing scheduler on NicePay's side).
- **Not mutually exclusive**: a merchant can use Checkout for one-time purchases and Recurring for subscriptions in the same integration.

<br>

### One thing both share, and one thing that differs

Both use the same [Authorization header](./info/nicepay-info-basic-token.md) and the same [Cancel](./api/nicepay-api-cancel.md) and [Transaction Status Inquiry](./api/nicepay-api-retrieve.md) APIs afterward. Their payment results (the Checkout `returnUrl` callback and the Recurring Payment responses), cancellation responses, Transaction Status Inquiry responses and webhooks carry a `signature` built with one rule: `hex(sha256(tid + amount + ediDate + SecretKey))`. The four values are joined as plain text with no separator, and the result is lowercase hexadecimal. See [Verifying the payment result](./api/nicepay-api-payment-window-url.md#verifying-the-payment-result) for a worked example. Other responses use other rules: Check Authorization Amount signs `tid + ediDate + SecretKey`, the card event and interest-free installment inquiries sign `ediDate + SecretKey`, and the token (`bid`) registration, deletion and status responses have no `signature`.
