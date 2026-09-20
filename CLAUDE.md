# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project purpose

This repository is the **English-language integration guide** for NicePay's "Untact" payment service, written for **overseas (non-Korean) merchants** integrating with NicePay's API. There is no application code here — every file is either a Markdown manual page or a supporting image. There is no build, lint, or test tooling (no `package.json`, no CI config); "development" in this repo means editing and reviewing Markdown.

## Repository structure

- `README.md` — the landing page. A card-grid index links out to every doc under `info/`, `api/`, and `code/`, plus a "Quick guide" section pointing to `INTEGRATION-PATHS.md` and `QUICKSTART.md`.
- `QUICKSTART.md` — the Checkout walkthrough (create a session, redirect, receive the returnUrl callback) with real curl request/response examples, split out of README so README can stay a pure index.
- `INTEGRATION-PATHS.md` — the "Which Integration Should I Use?" decision guide comparing Checkout, Key-in, and Recurring Payment before a merchant picks one.
- `info/` — conceptual/setup docs merchants need before making API calls: client/secret keys (`nicepay-info-key.md`), firewall & timeout requirements (`nicepay-info-firewall-timeout.md`), Basic/Bearer auth (`nicepay-info-basic-token.md`), supported browsers/environments (`nicepay-info-general.md`), and the Sandbox environment with worked examples (`nicepay-info-sandbox.md`).
- `api/` — one file per API domain: checkout/hosted payment page (`nicepay-api-payment-window-url.md`), recurring payments/tokens (`nicepay-api-billing.md`), access tokens (`nicepay-api-access-token.md`), status inquiry (`nicepay-api-retrieve.md`), cancel/refund (`nicepay-api-cancel.md`), webhooks (`nicepay-api-webhook.md`), reconciliation/settlement (`nicepay-api-reconciliation.md`), plus `nicepay-api-uri-list.md` which is a single master table of every endpoint (method, path, sandbox availability) linking into anchors in the other API files.
- `code/nicepay-code.md` — reference tables for HTTP status codes, card codes, bank codes, and API response codes, all cited from the `api/` docs via anchor links (e.g. `./code/nicepay-code.md#Card-code`).
- `image/` — SVG/PNG diagrams and screenshots embedded via `<img>` tags (not Markdown image syntax) in the docs above.

## Cross-file link conventions

Docs are heavily interlinked using relative paths + Markdown heading anchors (GitHub's auto-generated anchor slugs, e.g. `### Cancel Request parameter (with sessionId)` → `#cancel-request-parameter-with-sessionid`). When renaming or restructuring a heading, grep the whole repo for links to its anchor before changing it — nothing enforces link integrity here.

```bash
grep -rn "anchor-text-to-check" --include="*.md" .
```

Known gotcha (don't propagate the pattern): always link with a relative path (`./foo.md` or `../foo.md`), never a site-root absolute path (`/foo.md`). `jekyll-relative-links` only rewrites relative-style Markdown links to `.html`, and an absolute path also skips this project's GitHub Pages baseurl (`/nicepay-manual-eng/`) — the link 404s on the live site even though it looks fine in a raw GitHub file view or local Markdown preview. This exact mistake once broke every link in `api/nicepay-api-uri-list.md` and six links in `info/nicepay-info-sandbox.md`.

## Content conventions to preserve when editing

- **Heading depth is meaningful**: `##` marks a major API/feature section, `###` marks a request/response/example subsection, `####` marks a single nested response object (e.g. "Card information", "Coupon information"). Keep new content at the matching depth so anchors and the README/uri-list index stay predictable.
- **Type/nullability badges**: nested object/array fields are annotated with shields.io badges right in the heading, e.g. `#### Card information <img src="https://img.shields.io/badge/-Object-yellow"> <img src="https://img.shields.io/badge/-nullable-lightgrey">`. Reuse the existing badge URLs (`-Object-yellow`, `-Array-blueviolet`, `-nullable-lightgrey`, `-Beta version-red`) rather than inventing new colors/wording.
- **Callouts**: important caveats use a blockquote pattern, e.g.:
  ```
  > #### ⚠️ Important  
  > <warning text, each line ending with two trailing spaces for a <br>>
  ```
  Follow this exact shape (blockquote + bold-ish heading + trailing double-space line breaks) for new warnings.
- **Sandbox vs Live**: nearly every API doc distinguishes Sandbox and Live behavior/domains. When adding a new endpoint or example, state explicitly whether it's available in Sandbox (cross-reference `info/nicepay-info-sandbox.md` and the availability column in `api/nicepay-api-uri-list.md`).
- **Example payloads keep real API text as-is**: response examples embed literal Korean strings the API actually returns (e.g. `"resultMsg": "정상 처리되었습니다."`). Do not translate these — they document actual response content, not prose.
- **Images** are embedded with raw `<img src="./image/....svg" width="800px">` tags, not `![]()` syntax — match this when adding diagrams.

## Editing scope note

Since prose here is documentation for external merchants (not internal dev-facing text), edits should stay in English and match NicePay's existing technical-writing tone in this repo (concise, numbered/tabular parameter references, explicit Sandbox/Live callouts) rather than following any Korean prose style.
