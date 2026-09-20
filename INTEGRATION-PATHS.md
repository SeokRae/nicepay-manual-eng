## Which Integration Should I Use?

NicePay Untact offers three ways to charge a card. They're distinguished by **who holds the card details**, and that single question decides which one fits your use case.

| | Checkout | Key-in Payment | Recurring Payment |
|:---|:---|:---|:---|
| **Card details held by** | The customer, entered on NicePay's own hosted page | The merchant (MOTO / manually entered) | The merchant, once, to register a reusable token |
| **Entry point** | `POST /v1/checkout` | `POST /v1/key-in/payments` | `POST /v1/subscribe/regist` then `POST /v1/subscribe/{bid}/payments` |
| **Flow shape** | Asynchronous: redirect the customer, then receive a `returnUrl` callback | Synchronous: one request, one response | Synchronous: register once, charge many times |
| **Typical use case** | Standard online checkout, customer is present | Phone/mail orders, support-agent-assisted charges | Subscriptions, memberships, installment billing |
| **Sandbox available** | ○ | × * | ○ |
| **PCI scope for the merchant** | Minimal — your server never touches raw card numbers | Full — your server receives and encrypts raw card data | Full at registration, minimal afterward (only the token) |
| **Guide** | [Quick Start Guide](./QUICKSTART.md) | [Key-in Payment](./api/nicepay-api-keyin.md) | [Recurring Payment](./api/nicepay-api-billing.md) |

\* Key-in Payment is not provided in Sandbox; see [Sandbox](./info/nicepay-info-sandbox.md#base-url-information-for-sandbox-and-live) for the full per-API availability table.

<br>

> #### ⚠️ Important  
> Key-in requires your merchant account to be specifically enabled for manual-entry payments by NicePay, and it can't be tried out in Sandbox first — you can only test it against Live once that's enabled. See the [Key-in Payment](./api/nicepay-api-keyin.md) doc's Important note before planning around it.

<br>

### Still not sure?

- **Building a normal storefront where the customer checks out themselves?** Use **Checkout**. It's the default path this guide's [Quick Start Guide](./QUICKSTART.md) walks through, and it keeps raw card data off your server entirely.
- **Charging a card your support team already has on file (phone order, invoice payment)?** Use **Key-in**.
- **Billing the same customer repeatedly (subscription, membership)?** Use **Recurring Payment** to register a token once, then call the Payments API yourself each time a charge is due (there's no automatic billing scheduler on NicePay's side).
- **Not mutually exclusive** — a merchant can use Checkout for one-time purchases and Recurring for subscriptions in the same integration.

<br>

### One thing all three share, and one thing that differs

All three use the same [Authorization header](./info/nicepay-info-basic-token.md), the same `signature` verification rule on responses (`hex(sha256(tid + amount + ediDate + SecretKey))`, see any API doc's Response Parameter table), and the same [Cancel](./api/nicepay-api-cancel.md) and [Transaction Status Inquiry](./api/nicepay-api-retrieve.md) APIs afterward.

Where Key-in and Recurring differ from each other: **their `encData` card-detail encryption is not interchangeable**. Key-in uses AES/ECB (no IV); Recurring uses AES/CBC (needs an IV). Copying one's example into the other will fail decryption on NicePay's side. See each API's own `encData Field Encryption Example` section.
