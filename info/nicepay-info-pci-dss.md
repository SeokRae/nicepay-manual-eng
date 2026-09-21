# PCI-DSS Overview

**PCI-DSS** (Payment Card Industry Data Security Standard) is an international security standard maintained by the [PCI Security Standards Council](https://www.pcisecuritystandards.org/) (founded by Visa, Mastercard, American Express, Discover, and JCB). Any organization that stores, processes, or transmits cardholder data is contractually required by the card networks to comply with it, regardless of size or industry.

This page is general background on the standard itself, not NicePay-specific guidance. It exists because [Key-in Payment](../api/nicepay-api-keyin.md) puts your own server in the path of raw card numbers, which is a meaningfully bigger compliance footprint than [Checkout](../api/nicepay-api-payment-window-url.md), where card data never reaches your server at all.

> #### ⚠️ Important  
> The exact PCI-DSS level, SAQ type, and validation steps required for *your* integration with NicePay are determined by your merchant contract, not by this manual. [Open an issue on this manual's repository](https://github.com/SeokRae/nicepay-manual-eng/issues) if you'd like this page to link out to NicePay-specific guidance once it exists, or discuss requirements directly with NicePay when you contract for Key-in access.  

<br>

## Why it matters by integration path

| | Checkout | Key-in Payment | Recurring Payment |
|:---|:---|:---|:---|
| Does your server see the raw card number? | No — entered on NicePay's Hosted Payment Page | Yes — your server receives it and builds `encData` | Only once, at token registration |
| Rough PCI-DSS impact | Lowest (comparable to a redirect-based SAQ A scenario) | Highest (comparable to a card-not-present SAQ D scenario) | Low after registration, higher briefly during it |

<br>

## Merchant compliance levels

The card networks group merchants into levels by annual transaction volume. Exact thresholds vary slightly by card network (Visa/Mastercard/etc.), so treat these as the commonly used shape rather than a fixed universal number:

| Level | Typical annual transaction volume |
|:-----:|:-----------------------------------|
| 1 | ~6 million+ |
| 2 | ~1 million – 6 million |
| 3 | ~20,000 – 1 million (e-commerce) |
| 4 | Below level 3's threshold |

Higher levels generally require a formal on-site assessment by a Qualified Security Assessor (QSA); lower levels are typically eligible for self-assessment.

<br>

## Self-Assessment Questionnaire (SAQ) types

Merchants who don't need a full on-site QSA assessment validate compliance with one of several SAQ types, matched to how card data flows through their systems:

| SAQ type | Typical scenario |
|:--------:|:-------------------|
| A | Card data fully outsourced to a redirect/hosted payment page — the merchant's own systems never touch it |
| A-EP | Merchant's website controls how the payment page is presented (e.g. an iframe) but doesn't receive card data directly |
| B / B-IP | Card-present, using standalone or IP-connected payment terminals, no electronic cardholder data storage |
| C | Card-present, using a payment application connected to the internet |
| C-VT | Manually entering card data one transaction at a time into a virtual terminal, no electronic storage |
| P2PE-HW | Card data captured via a validated point-to-point encryption hardware solution |
| D | Everyone else — including merchants whose own servers receive, process, or store card data electronically (the typical bucket for a custom Key-in integration) |

<br>

## The 12 PCI-DSS requirements

PCI-DSS groups its requirements into 6 control objectives:

1. **Build and maintain a secure network** — install and maintain firewall configuration; don't use vendor-supplied defaults for passwords/security parameters.
2. **Protect cardholder data** — protect stored cardholder data; encrypt transmission of cardholder data across open, public networks.
3. **Maintain a vulnerability management program** — use and regularly update anti-malware; develop and maintain secure systems and applications.
4. **Implement strong access control** — restrict access to cardholder data by business need-to-know; identify and authenticate access to system components; restrict physical access to cardholder data.
5. **Regularly monitor and test networks** — track and monitor all access to network resources and cardholder data; regularly test security systems and processes.
6. **Maintain an information security policy** — maintain a policy that addresses information security for personnel and third parties.

<br>

## Where to go next

- Planning a Key-in integration? Start with [Key-in Payment](../api/nicepay-api-keyin.md) and its account-activation requirements.
- Want to avoid this scope entirely? [Checkout](../api/nicepay-api-payment-window-url.md) keeps raw card data off your server — see [Which Integration Should I Use?](../INTEGRATION-PATHS.md).
- Official standard documents and the full requirement list: [pcisecuritystandards.org](https://www.pcisecuritystandards.org/standards/).
