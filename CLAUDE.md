# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project purpose

This repository is the **English-language integration guide** for NicePay's "For Startups" payment service (the merchant-facing brand at [start.nicepay.co.kr](https://start.nicepay.co.kr/)), written for **overseas (non-Korean) merchants** integrating with NicePay's API. "Untact" is the underlying API gateway's technical name, not the public brand: see `code/nicepay-code.md`'s `U`-prefixed response codes and `api/nicepay-api-cancel.md` for that usage. There is no application code here: the content is Markdown manual pages plus supporting images, and "development" in this repo means editing and reviewing Markdown.

## Site build and publishing

The docs are published by GitHub Pages' **legacy Jekyll build** from the root of `main`, at <https://seokrae.github.io/nicepay-manual-eng/>. Three tracked files configure it:

- `_config.yml`: site `title`/`description` (both merchant-facing: they render as the sidebar header and the page `<title>`), `theme: minima`, the `nav:` tree that builds the left sidebar, and an `exclude:` list.
- `_layouts/default.html`: a custom layout that replaces minima's top header with a left sidebar built by iterating `site.nav`.
- `assets/main.scss`: `@import "minima"` followed by the sidebar and theme overrides.

Jekyll renders **every** Markdown file at the repo root, so an internal file added there is published like any manual page. This file is kept off the site by `exclude:` in `_config.yml`; add any other contributor-facing doc to that list too. Note that declaring `exclude` replaces Jekyll's default list rather than extending it, which is why the defaults are repeated there.

There is no Gemfile, `package.json`, lint, test, or `.github/` workflow. `jekyll-relative-links` (which rewrites `.md` links to `.html`) and `jekyll-titles-from-headings` (which derives a page title from its first heading, so no front matter is needed) come from the Pages build's default plugin set, not from anything declared here. The `/nicepay-manual-eng/` baseurl is injected by the Pages build too, and is intentionally absent from `_config.yml`.

GitHub.com's Markdown viewer and Pages' kramdown renderer do **not** agree on every construct, so a change that looks right in the GitHub file view can still break on the live site. Verify on the live site rather than in preview:

```bash
gh api repos/SeokRae/nicepay-manual-eng/pages/builds/latest --jq '{status,commit}'
```

Poll until `commit` matches HEAD and `status` is `built`, then load the page (bypassing browser cache).

## Repository structure

- `README.md`: the landing page. A card-grid index links out to every doc under `info/`, `api/`, and `code/`, plus a "Quick guide" section pointing to `INTEGRATION-PATHS.md` and `QUICKSTART.md`.
- `QUICKSTART.md`: the Checkout walkthrough (create a session, redirect, receive the returnUrl callback) with real curl request/response examples, split out of README so README can stay a pure index.
- `INTEGRATION-PATHS.md`: the "Which Integration Should I Use?" decision guide comparing Checkout, Key-in, and Recurring Payment before a merchant picks one.
- `info/`: conceptual/setup docs merchants need before making API calls: client/secret keys (`nicepay-info-key.md`), firewall & timeout requirements (`nicepay-info-firewall-timeout.md`), Basic/Bearer auth (`nicepay-info-basic-token.md`), supported browsers/environments (`nicepay-info-general.md`), the Sandbox environment with worked examples (`nicepay-info-sandbox.md`), and a PCI-DSS overview of each integration path's compliance scope (`nicepay-info-pci-dss.md`).
- `api/`: one file per API domain: checkout/hosted payment page (`nicepay-api-payment-window-url.md`), recurring payments/tokens (`nicepay-api-billing.md`), key-in/MOTO card payments where the merchant server encrypts card data into `encData` (`nicepay-api-keyin.md`, Live only, no Sandbox), access tokens (`nicepay-api-access-token.md`), status inquiry (`nicepay-api-retrieve.md`), cancel/refund (`nicepay-api-cancel.md`), webhooks (`nicepay-api-webhook.md`), reconciliation/settlement (`nicepay-api-reconciliation.md`), plus `nicepay-api-uri-list.md` which is a single master table of every endpoint (method, path, sandbox availability) linking into anchors in the other API files.
- `code/nicepay-code.md`: reference tables for HTTP status codes, card codes, bank codes, and API response codes, all cited from the `api/` docs via anchor links (e.g. `./code/nicepay-code.md#card-code`).
- `image/`: SVG/PNG diagrams and screenshots embedded via `<img>` tags (not Markdown image syntax) in the docs above.

### Adding a new doc

A new Markdown file is not reachable until it is registered in three places, none of which is enforced:

1. `_config.yml` `nav:`: the only thing that puts a page in the left sidebar. A page missing here is reachable only through direct links.
2. `README.md`: a resource card in the grid, with an `.html` href (see the link conventions below).
3. `api/nicepay-api-uri-list.md`: for an API doc, add every endpoint to the master table with its method, path, and Sandbox availability.

The sidebar label is the page's **first heading** (no front matter is used), so choose that heading with the sidebar in mind. Note that sidebar labels and README card labels are worded independently today, e.g. the sidebar says "URI LIST" where the README card says "List of API".

When sweeping wording across the repo (branding, terminology, style), grep beyond `*.md`: `_config.yml` and the text inside `image/*.svg` are merchant-visible too, and a `--include="*.md"` grep has missed them before.

## Cross-file link conventions

Docs are heavily interlinked using relative paths + Markdown heading anchors (GitHub's auto-generated anchor slugs, e.g. `### Cancel Request parameter (with sessionId)` → `#cancel-request-parameter-with-sessionid`). When renaming or restructuring a heading, grep the whole repo for links to its anchor before changing it. Nothing enforces link integrity here.

```bash
grep -rn "anchor-text-to-check" --include="*.md" .
```

**Gotcha 1, absolute paths.** Always link with a relative path (`./foo.md` or `../foo.md`), never a site-root absolute path (`/foo.md`). `jekyll-relative-links` only rewrites relative-style Markdown links to `.html`, and an absolute path also skips the `/nicepay-manual-eng/` baseurl: the link 404s on the live site even though it looks fine in a raw GitHub file view or local Markdown preview. This exact mistake once broke every link in `api/nicepay-api-uri-list.md` and six links in `info/nicepay-info-sandbox.md`.

**Gotcha 2, raw HTML anchors.** `jekyll-relative-links` rewrites only Markdown-syntax links (`[text](./foo.md)`). Raw HTML anchors, such as README's `<a class="resource-card" href="...">` cards, are passed through untouched, so those must point at the **rendered** path (`./info/foo.html`, `./code/nicepay-code.html#card-code`), never `.md`. A `.md` href still returns HTTP 200 on the live site (Pages serves the source file as `text/markdown`), so a status-code link check will not catch it. This broke the README card grid once.

**Gotcha 3, badges change the slug.** A badge `<img>` inside a heading leaves one trailing hyphen per badge in the generated anchor:

| Heading | Anchor |
| --- | --- |
| `#### Card information <img ...> <img ...>` | `#card-information--` |
| `### Create a webhook <img ...>` | `#create-a-webhook-` |

Adding or removing a badge therefore changes the anchor and silently breaks incoming links. Grep for the anchor before touching a badged heading. Keep heading text unique within a file as well: duplicates get `-1`/`-2` suffixes that repoint when sections move.

## Content conventions to preserve when editing

- **Heading depth is meaningful**: `#` or `##` opens a document or a major API/feature section (both are in use, and the file's first heading doubles as its sidebar label), `###` marks a request/response/example subsection, `####` marks a nested object in a parameter table, both response objects (e.g. "Card information", "Coupon information") and request option groups (e.g. payment-window-url's "Cards & Wallets"). Keep new content at the matching depth so anchors and the README/uri-list index stay predictable, and keep the levels consistent within a file.
- **Type/nullability badges**: nested object/array fields are annotated with shields.io badges right in the heading, e.g. `#### Card information <img alt="Object type" src="https://img.shields.io/badge/-Object-yellow"> <img alt="Nullable" src="https://img.shields.io/badge/-nullable-lightgrey">`. Reuse the existing badge URLs (`-Object-yellow`, `-Array-blueviolet`, `-nullable-lightgrey`, `-Beta version-red`) rather than inventing new colors/wording, and give every badge its `alt` (`Object type`, `Array type`, `Nullable`, `Beta version`).
- **Callouts**: important caveats use a blockquote pattern, e.g.:
  ```
  > #### ⚠️ Important  
  > <warning text, each line ending with two trailing spaces for a <br>>
  ```
  Follow this exact shape (blockquote + bold-ish heading + trailing double-space line breaks) for new warnings.
- **Sandbox vs Live**: nearly every API doc distinguishes Sandbox and Live behavior/domains. When adding a new endpoint or example, state explicitly whether it's available in Sandbox (cross-reference `info/nicepay-info-sandbox.md` and the availability column in `api/nicepay-api-uri-list.md`).
- **Example payloads keep real API text as-is**: response examples embed literal Korean strings the API actually returns (e.g. `"resultMsg": "정상 처리되었습니다."`). Do not translate these: they document actual response content, not prose.
- **Images** are embedded with raw `<img alt="<one-sentence description of what the diagram shows>" src="../image/....svg" width="800px">` tags (alt first, then src, always `width="800px"`), not `![]()` syntax. Every diagram needs a descriptive `alt`; only a purely decorative badge may use `alt=""`.
- **Diagrams**: `image/*.svg` files are flowcast-rendered static SVGs whose actors are named Customer, Merchant Server, and NicePay. Strip the renderer's Korean "IF 흐름도" badge text before committing, and never leave an unreferenced file in `image/`.
- **Tables**: kramdown needs a blank line before and after every table. GitHub.com renders the table fine without them, so this breakage is visible only on the live site.
- **Prose style**: no em dashes, and no hedging or filler phrasing. Overview subsections are spelled `Over-view`. Say "customer" for the payer, "Merchant Server" for the merchant backend, and "Hosted Payment Page" for the checkout screen. Where a doc needs a support channel, link to this repository's issue tracker (`https://github.com/SeokRae/nicepay-manual-eng/issues`) rather than telling merchants to "contact NicePay support".

## Editing scope note

Since prose here is documentation for external merchants (not internal dev-facing text), edits should stay in English and match NicePay's existing technical-writing tone in this repo (concise, numbered/tabular parameter references, explicit Sandbox/Live callouts) rather than following any Korean prose style.
