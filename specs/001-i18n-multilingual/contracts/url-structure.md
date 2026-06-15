# Contract: URL Structure & Routing

Base (production): `https://ivanlynch.github.io/billbird-docs/` (update if a custom domain/`CNAME` is added — see research R6).

## Canonical URL map

| Page | Spanish (default) | English | Brazilian Portuguese |
|------|-------------------|---------|----------------------|
| Home | `/es/` (`es/index.html`) | `/en/` | `/pt-br/` |
| Support | `/es/support.html` | `/en/support.html` | `/pt-br/support.html` |
| Terms | `/es/terms.html` | `/en/terms.html` | `/pt-br/terms.html` |
| Privacy | `/es/privacy.html` | `/en/privacy.html` | `/pt-br/privacy.html` |

## Root router — `docs/index.html`

Contract for the bare `/` entry point. Must:

1. Run a synchronous `<head>` script that resolves a target language and calls `location.replace(target + '/')`.
   - Resolution order: `localStorage['billbird-lang']` (if a supported code) → first supported match in `navigator.languages` (or `navigator.language`) using prefix rules (`en*`→`en`, `es*`→`es`, `pt*`→`pt-br`) → default `es`.
2. Include `<meta http-equiv="refresh" content="0; url=es/">` (placed after the script) as the no-JS default.
3. Include a `<noscript>` block with visible links to `es/`, `en/`, and `pt-br/`.
4. Contain no indexable marketing content (it is a router); set `<meta name="robots" content="noindex">` and point search engines to language homes via the per-page hreflang.

**Acceptance**:
- JS on, no stored pref, browser=`pt-BR` → lands on `/pt-br/`.
- JS on, stored pref=`en` → lands on `/en/` regardless of browser language.
- JS on, browser=`fr` → lands on `/es/`.
- JS off → meta-refresh lands on `/es/`; noscript links to all three are visible/clickable.
- No redirect loop; back button does not return to `/` (uses `location.replace`).

## Optional — `docs/404.html`

GitHub Pages serves `docs/404.html` for unknown paths. Provide a minimal page that links to the three language homes (and may auto-redirect to default). Not required for MVP but recommended.

## Path rules (Constitution II)

- All navigable links between pages use **relative** paths:
  - Within a language: `support.html`, `terms.html`, `privacy.html`, `index.html` / `./`.
  - Switcher across languages: `../en/<page>.html`, `../pt-br/<page>.html`, `../es/<page>.html`.
  - Shared assets from a language dir: `../icon.png`, `../styles.css`.
- Only SEO metadata (`canonical`, `hreflang`, `sitemap.xml`) uses **absolute** URLs built from the base.
