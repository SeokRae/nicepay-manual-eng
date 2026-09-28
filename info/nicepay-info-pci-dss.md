# PCI-DSS Overview

**PCI-DSS** (Payment Card Industry Data Security Standard) is an international security standard maintained by the [PCI Security Standards Council](https://www.pcisecuritystandards.org/) (founded by Visa, Mastercard, American Express, Discover, and JCB). Any organization that stores, processes, or transmits cardholder data is contractually required by the card networks to comply with it, regardless of size or industry.

This page is general background on the standard itself, not NicePay-specific guidance. It exists because [Key-in Payment](../api/nicepay-api-keyin.md) puts your own server in the path of raw card numbers, which is a meaningfully bigger compliance footprint than [Checkout](../api/nicepay-api-payment-window-url.md), where card data never reaches your server at all.

> **⚠️ Important:** The exact PCI-DSS level, SAQ type, and validation steps required for *your* integration with NicePay are determined by your merchant contract, not by this manual. [Open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) if you'd like this page to link out to NicePay-specific guidance once it exists, or discuss requirements directly with NicePay when you contract for Key-in access.  

<br>

## Why it matters by integration path

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
      <th scope="row">Does your server see the raw card number?</th>
      <td>No (entered on NicePay's Hosted Payment Page)</td>
      <td>Yes (your server receives it and builds <code>encData</code>)</td>
      <td>Yes, at registration, when your server calls <code>/v1/subscribe/regist</code> (your server builds <code>encData</code>, as in Key-in). Each charge sends only the token (<code>bid</code>).</td>
    </tr>
    <tr>
      <th scope="row">Rough PCI-DSS impact</th>
      <td>Lowest (comparable to a redirect-based SAQ A scenario)</td>
      <td>Highest (comparable to a card-not-present SAQ D scenario)</td>
      <td>Same as Key-in for the system that calls <code>/v1/subscribe/regist</code>.</td>
    </tr>
  </tbody>
</table>

<br>

## Merchant compliance levels

The card networks group merchants into levels by annual transaction volume. Exact thresholds vary slightly by card network (Visa/Mastercard/etc.), so treat these as the commonly used shape rather than a fixed universal number:

| Level | Typical annual transaction volume |
|:-----:|:-----------------------------------|
| 1 | ~6 million+ |
| 2 | ~1 million – 6 million |
| 3 | ~20,000 – 1 million (e-commerce) |
| 4 | Below level 3's threshold |

Level 1, the highest volume, generally requires a formal on-site assessment by a Qualified Security Assessor (QSA). Levels 2 to 4 are typically eligible for self-assessment.

<br>

## Self-Assessment Questionnaire (SAQ) types

Merchants who don't need a full on-site QSA assessment validate compliance with one of several SAQ types, matched to how card data flows through their systems. The types below follow PCI DSS v4.0:

| SAQ type | Typical scenario |
|:--------:|:-------------------|
| A | Card data fully outsourced: the merchant's website redirects to, or shows in an iframe, the payment page of a PCI DSS compliant provider, and the merchant's own systems never touch card data |
| A-EP | The merchant's website creates the payment form itself (for example a direct post or its own script) and sends card data straight to the provider, without receiving it |
| B / B-IP | Card-present, using standalone or IP-connected payment terminals, no electronic cardholder data storage |
| C | Card-present, using a payment application connected to the internet |
| C-VT | Manually entering card data one transaction at a time into a virtual terminal, no electronic storage |
| P2PE | Card-present, using a validated point-to-point encryption (P2PE) solution (formerly SAQ P2PE-HW) |
| D | Everyone else, including merchants whose own servers receive, process, or store card data electronically (the typical bucket for a custom Key-in integration) |

<br>

## PCI-DSS requirements

PCI DSS v4.0 has 12 requirements, grouped into 6 goals:

1. **Build and maintain a secure network and systems**: install and maintain network security controls (Requirement 1); apply secure configurations to all system components (Requirement 2).
2. **Protect account data**: protect stored account data (Requirement 3); protect cardholder data with strong cryptography during transmission over open, public networks (Requirement 4).
3. **Maintain a vulnerability management program**: protect all systems and networks from malicious software (Requirement 5); develop and maintain secure systems and software (Requirement 6).
4. **Implement strong access control measures**: restrict access to system components and cardholder data by business need to know (Requirement 7); identify users and authenticate access to system components (Requirement 8); restrict physical access to cardholder data (Requirement 9).
5. **Regularly monitor and test networks**: log and monitor all access to system components and cardholder data (Requirement 10); test the security of systems and networks regularly (Requirement 11).
6. **Maintain an information security policy**: support information security with organizational policies and programs (Requirement 12).

<br>

## Where to go next

- Planning a Key-in integration? Start with [Key-in Payment](../api/nicepay-api-keyin.md) and its account-activation requirements.
- Want to avoid this scope entirely? [Checkout](../api/nicepay-api-payment-window-url.md) keeps raw card data off your server. See [Which Integration Should I Use?](../INTEGRATION-PATHS.md).
- Official standard documents and the full requirement list: [pcisecuritystandards.org](https://www.pcisecuritystandards.org/standards/).
