<h1 align="center">
  NicePay For Startups
</h1>

<p align="center">The English integration guide for NicePay For Startups, for merchants outside Korea.</p>

Reading this on GitHub? Open the guide at [seokrae.github.io/nicepay-manual-eng](https://seokrae.github.io/nicepay-manual-eng/): the links below work only on the site.

<br>


## Quick guide

New to NicePay For Startups? NicePay is a Korean payment gateway. You sign up for NicePay For Startups on the [admin console](https://start.nicepay.co.kr/merchant/login/main.do) and take your keys from there, see [Client and Secret key](./info/nicepay-info-key.md). The API has three integration paths. Checkout takes cards, bank transfer, virtual accounts, mobile phone billing and Korean easy-pay wallets through its `method` parameter. Key-in Payment charges cards, and Recurring Payment charges a card or easy-pay wallet registered once as a token. Not sure which fits your service? See **[Which Integration Should I Use?](./INTEGRATION-PATHS.md)**.

Already know you want a standard hosted checkout? Start here: **[Quick Start Guide](./QUICKSTART.md)** (create your first Checkout session and receive a test payment result in about 10 minutes).

Built your integration from an earlier version of this manual? The **[Changelog](./CHANGELOG.md)** lists every change that affects an integration.

<br><br>

## Information 
This is a common guide needed before development.

<div class="resource-grid">
  <a class="resource-card" href="./info/nicepay-info-key.html">Client and Secret key</a>
  <a class="resource-card" href="./info/nicepay-info-firewall-timeout.html">Firewall and Timeout</a>
  <a class="resource-card" href="./info/nicepay-info-basic-token.html">Basic and Bearer authentication</a>
  <a class="resource-card" href="./info/nicepay-info-general.html">Support environment</a>
  <a class="resource-card" href="./info/nicepay-info-sandbox.html">Sandbox</a>
  <a class="resource-card" href="./info/nicepay-info-pci-dss.html">PCI-DSS Overview</a>
  <a class="resource-card" href="./CHANGELOG.html">Changelog</a>
</div>

## API
This is a technical document that includes information about the API.

<div class="resource-grid">
  <a class="resource-card" href="./api/nicepay-api-uri-list.html">List of API</a>
  <a class="resource-card" href="./api/nicepay-api-payment-window-url.html">Checkout</a>
  <a class="resource-card" href="./api/nicepay-api-billing.html">Recurring Payment</a>
  <a class="resource-card" href="./api/nicepay-api-keyin.html">Key-in Payment</a>
  <a class="resource-card" href="./api/nicepay-api-access-token.html">Access token</a>
  <a class="resource-card" href="./api/nicepay-api-retrieve.html">Transaction Status Inquiry</a>
  <a class="resource-card" href="./api/nicepay-api-cancel.html">Cancel</a>
  <a class="resource-card" href="./api/nicepay-api-reconciliation.html">Reconciliation</a>
  <a class="resource-card" href="./api/nicepay-api-webhook.html">Webhook</a>
</div>

## Sample code (Korean, 2021)
These repositories hold Korean-language sample code from 2021, written for NicePay's Korean manual: the JavaScript payment window (`AUTHNICE`) and billing. They do not call the Checkout API in this guide, so use them only as a general reference.

<div align="left">
 <a href="https://github.com/nicepayments/nicepay-node">
  <img alt="Node.js sample code" src="https://img.shields.io/badge/node.js-2E7D32?style=for-the-badge&logo=node.js&logoColor=white">
 </a>
 <a href="https://github.com/nicepayments/nicepay-python">
  <img alt="Python sample code" src="https://img.shields.io/badge/python-3776AB?style=for-the-badge&logo=python&logoColor=white">
 </a>
 <a href="https://github.com/nicepayments/nicepay-ruby">
  <img alt="Ruby sample code" src="https://img.shields.io/badge/ruby-CC342D?style=for-the-badge&logo=ruby&logoColor=white">
 </a>
 <a href="https://github.com/nicepayments/nicepay-asp">
  <img alt="ASP sample code" src="https://img.shields.io/badge/asp-007396?style=for-the-badge&logo=&logoColor=white">
 </a>
 <a href="https://github.com/nicepayments/nicepay-java">
  <img alt="Java sample code" src="https://img.shields.io/badge/java-F7DF1E?style=for-the-badge&logo=&logoColor=white">
 </a>
 <a href="https://github.com/nicepayments/nicepay-php">
  <img alt="PHP sample code" src="https://img.shields.io/badge/php-5B5FA0?style=for-the-badge&logo=php&logoColor=white">
 </a>
 <a href="https://github.com/nicepayments/nicepay-dotnet">
  <img alt=".NET sample code" src="https://img.shields.io/badge/.net-512BD4?style=for-the-badge&logo=.net&logoColor=white">
 </a>
</div>

<br>

## Code
These are response and error codes.

<div class="resource-grid">
  <a class="resource-card" href="./code/nicepay-code.html#http-status-code">HTTP status code</a>
  <a class="resource-card" href="./code/nicepay-code.html#card-code">Card-code</a>
  <a class="resource-card" href="./code/nicepay-code.html#bank-code">Bank-code</a>
  <a class="resource-card" href="./code/nicepay-code.html#api-response-code">API Response code</a>
</div>

<br>