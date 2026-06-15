# Implementation Plan: Multilingual Site (i18n) — English, Spanish, Brazilian Portuguese

**Branch**: `001-i18n-multilingual` | **Date**: 2026-06-15 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `/specs/001-i18n-multilingual/spec.md`

## Summary

Make the Billbird marketing/legal site fully available in English, Spanish, and Brazilian Portuguese across all four public pages (home, support, terms, privacy). The chosen approach keeps the site a **static, zero-build site** by publishing one fully-translated static page per language under per-language directories (`/es/`, `/en/`, `/pt-br/`), with Spanish as the default/fallback. Language **switching** is done with plain `<a>` links (no JavaScript required). A small, progressively-enhanced script at the site root performs first-visit **browser-language auto-detection** and remembers the last chosen language for return visits; a `<meta refresh>` + `<noscript>` fallback guarantees no-JS visitors still land on (and can reach) every language. Per-page `hreflang`/canonical metadata and a sitemap make each language version discoverable and shareable.

## Technical Context

**Language/Version**: HTML5 + CSS3; minimal vanilla JavaScript (ES5-safe, no framework) only for root auto-detect/persistence

**Primary Dependencies**: None for runtime logic. Existing third-party: Google Fonts CDN (`DM Sans`, `DM Serif Display`) — already in use, retained.

**Storage**: Browser `localStorage` (single key `billbird-lang`) for language preference persistence. No server-side storage.

**Testing**: Manual browser verification per `quickstart.md` (open each page/language, toggle JS, set browser language). No automated test framework (consistent with the existing zero-build site).

**Target Platform**: Static files served by GitHub Pages from `docs/` on the default branch; modern mobile + desktop browsers.

**Project Type**: Static website (multi-page, no build pipeline).

**Performance Goals**: Pages render effectively instantly; language switch is a normal page navigation (< 1s). No added blocking scripts; auto-detect redirect runs synchronously in `<head>` of the root document only.

**Constraints**: Zero build step; GitHub Pages static hosting; relative paths for all navigable links/assets; prefer no JavaScript (any JS must be progressive enhancement with a no-JS fallback); content must be fully readable with JS disabled.

**Scale/Scope**: 4 pages × 3 languages = 12 content pages, plus 1 root redirector, 1 shared stylesheet, 1 sitemap, 1 (optional) 404 redirector. Single maintainer.

## Constitution Check

*GATE: evaluated against `.specify/memory/constitution.md` v1.0.0.*

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Static-First, Zero Build | ✅ PASS | Output stays plain `.html` + a static `.css`; no bundler/transpiler/build step introduced. |
| II. GitHub Pages Deployment Integrity | ⚠️ PASS w/ justified exception | All files live under `docs/`; all **navigable** links and asset refs stay relative. `hreflang`/`canonical`/`sitemap` use absolute production URLs — required by the SEO spec (FR-010) and not satisfiable with relative paths. See Complexity Tracking. |
| III. Accessibility & Semantic HTML | ✅ PASS | Each page declares the correct `lang`; language switcher is keyboard-reachable `<a>` links with accessible labels and an indicated active state; headings/`alt`/contrast preserved. |
| IV. Lightweight & Self-Contained Delivery | ⚠️ PASS w/ justified exception | A small vanilla JS snippet is added for first-visit auto-detect + preference persistence. Browser-language detection is impossible in pure HTML/CSS on static hosting. Switching itself uses no JS; `<meta refresh>`/`<noscript>` fallback provided; no analytics/tracking added. See Complexity Tracking. |
| V. Content, Brand & Legal Accuracy | ✅ PASS (resolved) | Constitution amended to v1.1.0: Principle V now defines the multilingual model (all pages in EN/ES/PT-BR, Spanish default/fallback, legal translations preserve meaning + state effective date and governing language). This feature aligns with the amended principle. |

**Gate result**: PASS with documented, justified deviations (Complexity Tracking below). The earlier Principle V governance follow-up is resolved — Constitution amended to v1.1.0 via `/speckit-constitution`. No unjustified violations block planning.

## Project Structure

### Documentation (this feature)

```text
specs/001-i18n-multilingual/
├── plan.md              # This file
├── spec.md              # Feature specification
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output
├── quickstart.md        # Phase 1 output
├── contracts/           # Phase 1 output
│   ├── url-structure.md
│   ├── language-switcher.md
│   └── seo-metadata.md
├── checklists/
│   └── requirements.md
└── tasks.md             # Created later by /speckit-tasks
```

### Source Code (repository root)

```text
docs/
├── index.html            # Root language redirector (auto-detect + meta-refresh/noscript fallback → /es/)
├── 404.html              # (optional) custom 404 that redirects to default language
├── styles.css            # Extracted shared styles (deduplicated from the current inline <style> blocks)
├── sitemap.xml           # Lists all 12 language URLs for discoverability
├── icon.png              # Shared asset (referenced from language dirs as ../icon.png)
├── es/                   # Spanish (default/fallback)
│   ├── index.html
│   ├── support.html
│   ├── terms.html
│   └── privacy.html
├── en/                   # English
│   ├── index.html
│   ├── support.html
│   ├── terms.html
│   └── privacy.html
└── pt-br/                # Brazilian Portuguese
    ├── index.html
    ├── support.html
    ├── terms.html
    └── privacy.html
```

**Structure Decision**: Multi-page static site with **one directory per language** under `docs/`. The current root pages (`docs/index.html`, `support.html`, `terms.html`, `privacy.html`) move into `docs/es/` (their content is Spanish/becomes the Spanish version), with English and Brazilian Portuguese siblings created under `docs/en/` and `docs/pt-br/`. The existing duplicated inline CSS is extracted into a single `docs/styles.css` (referenced relatively as `../styles.css`) so the 12 pages differ only in text content, drastically reducing translation/maintenance surface. `docs/index.html` becomes a thin language router. This is the only model that satisfies "static + zero-build + works without JS + shareable per-language URLs + correct per-page `lang`/hreflang" simultaneously.

## Complexity Tracking

> Justified deviations from the constitution.

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| Minimal JavaScript at site root (auto-detect + `localStorage` persistence) | FR-004/US3 require first-visit detection of the browser's preferred language and FR-006 requires remembering the choice across return visits; neither is expressible in pure HTML/CSS on static hosting | A pure-HTML approach can only ever serve one fixed default at the root, failing auto-detect. The JS is progressive enhancement (switching works without it) and a `<meta refresh>`/`<noscript>` fallback preserves the no-JS experience. |
| Absolute URLs in `hreflang`/`canonical`/`sitemap` | FR-010 (search engines associate alternate language versions) and the `hreflang`/canonical specs require fully-qualified URLs | Relative `hreflang` is not honored by search engines, so SEO discoverability (a stated requirement) would silently fail. Limited strictly to SEO metadata; all human-navigable links/assets remain relative per Principle II. |
| Per-language content duplication (12 pages) | Each language needs an independently addressable, fully-static, no-JS-readable page | A JS dictionary/runtime-swap single page would break no-JS readability, correct per-page `lang`, shareable URLs, and SEO. CSS is deduplicated into `styles.css` to keep duplication limited to translatable text only. |
