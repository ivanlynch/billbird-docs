# Phase 0 Research: Multilingual Site (i18n)

All Technical Context unknowns are resolved below. The site is constrained to a zero-build static deployment on GitHub Pages, which drives every decision.

## R1. Multilingual delivery model for a zero-build static site

- **Decision**: Pre-render one fully-translated static HTML page per language, organized under per-language directories (`/es/`, `/en/`, `/pt-br/`). No runtime text substitution.
- **Rationale**: Satisfies all constraints at once — no build step, each page is fully readable with JS disabled (SC-007), each page declares its own correct `lang` (FR-007), each language version has its own shareable URL (FR-009), and SEO sees distinct documents (FR-010). Fallback for a missing translation is simply authoring that page from the Spanish source (FR-011).
- **Alternatives considered**:
  - *Single page + JS dictionary swap*: rejected — blank/untranslated without JS (violates Principle IV and SC-007), one URL (fails FR-009), dynamic `lang` (weak FR-007), poor SEO.
  - *Static site generator / build templating*: rejected — introduces a build step (violates Principle I, zero-build).
  - *Server-side content negotiation (Accept-Language)*: rejected — GitHub Pages has no server-side runtime.

## R2. Default language and fallback

- **Decision**: Spanish (`es`) is the default and the fallback for unsupported browser languages and for any not-yet-translated string.
- **Rationale**: Matches spec FR-005 and the product's current primary (Spanish-speaking) market; the existing root content is already Spanish, minimizing churn.
- **Alternatives considered**: English default — rejected; would demote the primary market and contradict the spec.

## R3. First-visit language auto-detection

- **Decision**: At the site root (`docs/index.html`), a tiny synchronous `<head>` script picks the target language as: stored preference (`localStorage['billbird-lang']`) → first match in `navigator.languages` against the supported set (any `pt*` → `pt-br`) → else `es`, then `location.replace('<lang>/')`. A `<meta http-equiv="refresh" content="0; url=es/">` plus a `<noscript>` block with visible links to all three languages provides the no-JS fallback (defaults to Spanish, all languages reachable).
- **Rationale**: Browser-language detection is impossible in pure HTML/CSS (Complexity Tracking). Doing it only at the root keeps all content pages script-light. The meta-refresh + noscript path keeps SC-007 (no-JS) satisfied. `location.replace` (not `assign`) avoids polluting browser history.
- **Alternatives considered**: Detecting on every page — rejected; unnecessary, slower, and risks redirect loops. Cookie-based storage — rejected; `localStorage` is simpler and no server reads it.
- **Edge handling**: `navigator.languages` absent → fall back to `navigator.language` → else `es`. Match is prefix-based (`en-US`→`en`, `pt-PT`/`pt`→`pt-br`).

## R4. Language switching and persistence

- **Decision**: The language switcher on every content page is a set of plain `<a>` links to the equivalent page in each other language (e.g. on `/es/support.html`: links to `/en/support.html`, `/pt-br/support.html`). Each content page sets `localStorage['billbird-lang']` to its own language on load (one line of JS) so the root router remembers the last-used language on return visits. The active language is shown as a non-link/visually-marked item.
- **Rationale**: Switching works with zero JS (FR-003/FR-008/FR-009 satisfied structurally by relative links). Persistence (FR-006) is a progressive enhancement layered on top; if JS is off, switching still works, only return-visit memory degrades to the default — acceptable per SC-007.
- **Alternatives considered**: JS-driven switcher that rewrites the URL — rejected; needlessly requires JS for a core action.

## R5. SEO discoverability of language versions

- **Decision**: Every page includes `<link rel="canonical">` (self, absolute) and `<link rel="alternate" hreflang="...">` for `es`, `en`, `pt-br`, plus `hreflang="x-default"` → the Spanish version. A `docs/sitemap.xml` lists all 12 language URLs. Each page also sets `<html lang>` and an appropriate `<meta name="description">` in the page language.
- **Rationale**: Implements FR-010 and supports US3. `hreflang`/canonical require absolute URLs (Complexity Tracking).
- **Alternatives considered**: Relative `hreflang` — rejected; not honored by search engines.

## R6. Production base URL (for canonical/hreflang/sitemap)

- **Decision**: Use `https://ivanlynch.github.io/billbird-docs/` as the base URL. The repository is `github.com/ivanlynch/billbird-docs` and there is no `CNAME`, so this is the live GitHub Pages URL.
- **Rationale**: Required to build absolute SEO URLs. Centralize the base in one documented place so it is trivial to update if a custom domain is later added (which would add a `CNAME` and change the base).
- **Alternatives considered**: Guessing a custom domain — rejected; none is configured.
- **Note**: If a custom domain is added later, update the base URL in canonical/hreflang/sitemap and add `docs/CNAME`.

## R7. CSS duplication / maintainability

- **Decision**: Extract the shared inline `<style>` (currently duplicated per page) into a single `docs/styles.css`, referenced from language pages as `../styles.css`. Page-specific rules that genuinely differ (e.g. the long-form legal pages vs. the landing page) may stay scoped within that file or in small per-page blocks, but the bulk is shared.
- **Rationale**: With 12 pages, duplicated inline CSS would be a maintenance hazard; a static `.css` file is explicitly allowed by Principle I. Keeping styling shared means the 12 pages differ only by translated text, which is the goal.
- **Alternatives considered**: Keep CSS inline per page — rejected; 12× duplication, error-prone.

## R8. Translation source of legal pages

- **Decision**: Translate terms and privacy for comprehension; each legal page states its effective date (currently `2026-03-22`) and declares the governing/authoritative language. Treat the English version as the reference unless the maintainer/legal counsel decides otherwise.
- **Rationale**: Implements FR-012 and respects Constitution Principle V's accuracy mandate while extending it to multiple languages. The current legal copy is generated boilerplate referencing the same effective date across pages.
- **Alternatives considered**: Leave legal pages English-only — rejected; contradicts the multilingual goal (FR-001) and would produce mixed-language journeys. Flagged for a Principle V amendment.
