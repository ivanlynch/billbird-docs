# Contract: SEO & Per-Page Metadata

Implements FR-007 (correct `lang`), FR-010 (discoverable alternates), and US3 (shareability). Base URL: `https://ivanlynch.github.io/billbird-docs/`.

## Per content page (`<head>`) requirements

Each of the 12 content pages MUST include:

1. `<html lang="...">` — `es`, `en`, or `pt-BR` (note casing) matching the content.
2. `<title>` and `<meta name="description">` — written in the page's language.
3. `<meta charset="UTF-8">` and responsive `<meta name="viewport">` (Constitution constraint).
4. **Canonical** (absolute, self):
   - e.g. `es/support.html` → `<link rel="canonical" href="https://ivanlynch.github.io/billbird-docs/es/support.html">`.
5. **hreflang alternates** (absolute) — all three siblings + `x-default`:
   ```html
   <link rel="alternate" hreflang="es"        href="<base>/es/<page>.html">
   <link rel="alternate" hreflang="en"        href="<base>/en/<page>.html">
   <link rel="alternate" hreflang="pt-br"     href="<base>/pt-br/<page>.html">
   <link rel="alternate" hreflang="x-default" href="<base>/es/<page>.html">
   ```
   (For the home page, `<page>.html` is `index.html`; the directory form `/es/` is the canonical.)

## Sitemap — `docs/sitemap.xml`

- Lists all 12 language URLs (absolute).
- Each `<url>` SHOULD include `<xhtml:link rel="alternate" hreflang="...">` entries mirroring the per-page hreflang set.

## Root router metadata

- `docs/index.html` is `noindex` (it is a redirect, not content). Discovery of content happens via the language homes' hreflang + sitemap.

## Legal-page metadata (FR-012)

- Terms/Privacy pages in every language MUST display the effective date (currently `2026-03-22`) and a short governing-language notice (e.g. "In case of discrepancy, the English version prevails." localized), per research R8.

## Acceptance

- Each page's `<html lang>` matches its content and reciprocal hreflang links resolve (each language references the other two + itself + x-default).
- `canonical` is self-referential and absolute on all 12 pages.
- `sitemap.xml` validates and contains 12 URLs.
- Root `/` is marked `noindex`.
