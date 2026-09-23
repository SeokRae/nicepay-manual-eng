## Which Integration Should I Use?

NicePay For Startups offers three ways to charge a card. They're distinguished by **who holds the card details**, and that single question decides which one fits your use case.

<table>
  <thead>
    <tr>
      <th scope="col">Aspect</th>
      <th scope="col">Checkout</th>
      <th scope="col">Key-in Payment</th>
      <th scope="col">Recurring Payment</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Card details held by</th>
      <td>The customer, entered on NicePay's Hosted Payment Page</td>
      <td>The merchant (MOTO / manually entered)</td>
      <td>The merchant, once, to register a reusable token</td>
    </tr>
    <tr>
      <th scope="row">Entry point</th>
      <td><code>POST /v1/checkout</code></td>
      <td><code>POST /v1/key-in/payments</code></td>
      <td><code>POST /v1/subscribe/regist</code> then <code>POST /v1/subscribe/{bid}/payments</code></td>
    </tr>
    <tr>
      <th scope="row">Flow shape</th>
      <td>Asynchronous: redirect the customer, then receive a <code>returnUrl</code> callback</td>
      <td>Synchronous: one request, one response</td>
      <td>Synchronous: register once, charge many times</td>
    </tr>
    <tr>
      <th scope="row">Typical use case</th>
      <td>Standard online checkout, customer is present</td>
      <td>Phone/mail orders, support-agent-assisted charges</td>
      <td>Subscriptions, memberships, installment billing</td>
    </tr>
    <tr>
      <th scope="row">Sandbox available</th>
      <td>Yes</td>
      <td>No</td>
      <td>Yes</td>
    </tr>
    <tr>
      <th scope="row">PCI scope for the merchant</th>
      <td>Minimal: your server never touches raw card numbers</td>
      <td><a href="./info/nicepay-info-pci-dss.html">Full</a>: your server receives and encrypts raw card data</td>
      <td>Full at registration, minimal afterward (only the token)</td>
    </tr>
    <tr>
      <th scope="row">Guide</th>
      <td><a href="./QUICKSTART.html">Quick Start Guide</a></td>
      <td><a href="./api/nicepay-api-keyin.html">Key-in Payment</a></td>
      <td><a href="./api/nicepay-api-billing.html">Recurring Payment</a></td>
    </tr>
  </tbody>
</table>

Key-in Payment is not provided in Sandbox; see [Sandbox](./info/nicepay-info-sandbox.md#base-url-information-for-sandbox-and-live) for the full per-API availability table.

<br>

> **⚠️ Important:** Key-in requires your merchant account to be specifically enabled for manual-entry payments by NicePay, and it can't be tried out in Sandbox first. You can only test it against Live once that's enabled.  
> See the [Key-in Payment](./api/nicepay-api-keyin.md) doc's Important note before planning around it.

<br>

### Still not sure?

- **Building a normal storefront where the customer checks out themselves?** Use **Checkout**. It's the default path this guide's [Quick Start Guide](./QUICKSTART.md) walks through, and it keeps raw card data off your server entirely.
- **Charging a card your support team already has on file (phone order, invoice payment)?** Use **Key-in**.
- **Billing the same customer repeatedly (subscription, membership)?** Use **Recurring Payment** to register a token once, then call the Payments API yourself each time a charge is due (there's no automatic billing scheduler on NicePay's side).
- **Not mutually exclusive**: a merchant can use Checkout for one-time purchases and Recurring for subscriptions in the same integration.

<br>

### One thing all three share, and one thing that differs

All three use the same [Authorization header](./info/nicepay-info-basic-token.md), the same `signature` verification rule on responses (`hex(sha256(tid + amount + ediDate + SecretKey))`, see any API doc's Response Parameter table), and the same [Cancel](./api/nicepay-api-cancel.md) and [Transaction Status Inquiry](./api/nicepay-api-retrieve.md) APIs afterward.

Where Key-in and Recurring differ from each other: **their `encData` card-detail encryption is not interchangeable**. Key-in uses AES/ECB (no IV); Recurring uses AES/CBC (needs an IV). Copying one's example into the other will fail decryption on NicePay's side. See each API's own `encData Field Encryption Example` section.
