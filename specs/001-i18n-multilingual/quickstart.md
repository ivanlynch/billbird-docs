# Quickstart: Validate the Multilingual Site

This is a static, zero-build site. "Running" it means serving the `docs/` folder and opening it in a browser. No install step.

## Prerequisites

- A modern browser.
- A way to serve `docs/` over HTTP (so relative paths + `localStorage` behave like production). Any one of:
  - `python3 -m http.server 8000 --directory docs` then open `http://localhost:8000/`
  - or open files directly via `file://` (most checks work; auto-redirect/persistence are best verified over HTTP).

## Reference artifacts

- URL map & router behavior: [contracts/url-structure.md](./contracts/url-structure.md)
- Switcher behavior/markup: [contracts/language-switcher.md](./contracts/language-switcher.md)
- SEO/metadata rules: [contracts/seo-metadata.md](./contracts/seo-metadata.md)
- Content/string completeness rules: [data-model.md](./data-model.md)

## Validation scenarios

### 1. Full translation per language (US1 / SC-001, SC-006)
For each language `es`, `en`, `pt-br` and each page `index`, `support`, `terms`, `privacy`:
- Open `/<lang>/<page>.html`.
- **Expect**: every visible string (headings, body, buttons, nav, footer) and the browser-tab title are in that language; `<html lang>` matches; no second language appears.

### 2. Switching + persistence (US2 / SC-002, SC-004)
- Open `/es/` → use the language switcher → choose **English**.
- **Expect**: lands on `/en/` (same page), content fully English, English marked active.
- Navigate to Support, Privacy, Terms via the page links.
- **Expect**: each stays in English (relative links keep the language).
- Return to the bare site root `/` (new tab over HTTP).
- **Expect**: router restores **English** (stored preference).

### 3. First-visit auto-detection (US3 / SC-003)
- Clear `localStorage` for the site (DevTools → Application → Local Storage → delete `billbird-lang`).
- Set the browser's preferred language to Portuguese (Brazil) and open `/`.
- **Expect**: lands on `/pt-br/`.
- Repeat with browser language = English → `/en/`; with an unsupported language (e.g. French) → `/es/`.

### 4. No-JavaScript fallback (SC-007)
- Disable JavaScript in the browser, open `/`.
- **Expect**: meta-refresh lands on `/es/`; the `<noscript>` links to all three languages are visible and clickable; every `/<lang>/<page>.html` is fully readable; the switcher links still work.

### 5. Shareable language links (US3 / SC-005)
- Copy `/<base>/en/support.html` into a fresh browser profile (no stored pref).
- **Expect**: opens directly in English support (the deep link wins regardless of detection).

### 6. SEO metadata (FR-010)
- View source of any content page.
- **Expect**: self `canonical` (absolute), `hreflang` links for `es`/`en`/`pt-br`/`x-default`, language-appropriate `<title>`/description.
- Open `/sitemap.xml` → **expect** 12 absolute URLs. Open `/` source → **expect** `noindex`.

### 7. Legal accuracy (FR-012)
- Open terms + privacy in each language.
- **Expect**: effective date present (`2026-03-22`), brand/contact (`ilynchdev@gmail.com`) consistent, governing-language notice present, legal meaning preserved vs. the English reference.

### 8. Constitution regression check
- All files live under `docs/`; navigable links/assets are relative; no analytics/tracking added; only the documented minimal JS is present; pages responsive on mobile + desktop.

## Done when

All eight scenarios pass. Then proceed to `/speckit-tasks`.
