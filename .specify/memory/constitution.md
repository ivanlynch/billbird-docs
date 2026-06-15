<!--
SYNC IMPACT REPORT
==================
Version change: 1.0.0 → 1.1.0
Bump rationale: MINOR — materially expanded Principle V to define the multilingual
(i18n) content model. The previous fixed per-page language rule (Spanish for
marketing/support, English for legal) is superseded by a multilingual model in
which every public page is published in English, Spanish, and Brazilian
Portuguese with Spanish as the default/fallback. No principle removed; intent of
Principle V (accuracy, brand, legal integrity) preserved and broadened.

Modified principles:
  V. Content, Brand & Legal Accuracy (expanded: multilingual content model added)
Added principles: none
Added sections: none
Removed sections: none

Templates requiring updates:
  ✅ .specify/templates/plan-template.md      (generic Constitution Check; no change needed)
  ✅ .specify/templates/spec-template.md       (no mandatory section conflicts)
  ✅ .specify/templates/tasks-template.md      (no principle-driven task categories changed)
  ✅ .specify/memory/constitution.md           (this file)

Deferred TODOs: none
-->

# Billbird Docs Constitution

Billbird Docs is the public marketing and legal website for the Billbird app (a
local-first subscription tracker). It is a static site served from the `docs/`
directory via GitHub Pages. This constitution governs how the site is built,
changed, and shipped.

## Core Principles

### I. Static-First, Zero Build

The site MUST remain plain static HTML and CSS that the browser can render
directly from source, with no build step, bundler, transpiler, or server-side
runtime. Pages are authored as self-describing `.html` files (styling MAY live
inline in a `<style>` block or in a static `.css` file). No tooling step may be
required between editing a file and it being deployable.

Rationale: GitHub Pages serves files as-is. Eliminating a build pipeline keeps
the project trivially reproducible, reviewable as raw diffs, and free of
dependency drift for a site maintained by a single author.

### II. GitHub Pages Deployment Integrity

The published root is the `docs/` directory on the default branch; every file
intended to be public MUST live there. All internal links and asset references
(pages, `icon.png`, fonts, styles) MUST resolve correctly when served from the
GitHub Pages URL, using relative paths only — never absolute filesystem paths or
`localhost`. A change is not "done" until it loads correctly as a static file
opened from `docs/`.

Rationale: The only deployment surface is GitHub Pages. Broken relative paths or
files placed outside `docs/` silently 404 in production even when they work
locally.

### III. Accessibility & Semantic HTML

Markup MUST be semantic and accessible: a single meaningful `<title>`, correct
`lang` attribute per page, `alt` text on all informative images, logical heading
order, and sufficient color contrast against the defined palette. Interactive
and navigational elements MUST be reachable and operable without relying on
color alone.

Rationale: A public-facing site must be usable by everyone and indexable by
search engines; semantic HTML is the cheapest durable guarantee of both.

### IV. Lightweight & Self-Contained Delivery

Pages MUST stay lightweight and minimize external runtime dependencies. Third-
party requests are limited to clearly justified essentials (e.g. the web-font
CDN already in use). No analytics, tracking pixels, ad scripts, or behavioral
telemetry may be added — this is enforced both for performance and because it
would contradict the product's stated privacy posture. Prefer no JavaScript;
add it only when a feature cannot be achieved with HTML/CSS.

Rationale: A fast, dependency-light site loads reliably worldwide and keeps the
marketing site consistent with Billbird's "local-first, we don't track you"
promise.

### V. Content, Brand & Legal Accuracy

Content MUST be accurate, on-brand, and internally consistent:
- The site is multilingual: every public page MUST be published in English,
  Spanish, and Brazilian Portuguese. Spanish is the default and the fallback
  language (used when a visitor's preferred language is unsupported and for any
  not-yet-translated string). Each page version MUST set a `lang` attribute that
  matches the language of its content, and no page may mix languages.
- Every user-facing string — headings, body, controls/navigation, and metadata
  (page title and description) — MUST be translated for each of the three
  languages; a missing translation MUST fall back to the Spanish source rather
  than display blank or broken content.
- Brand naming ("Billbird"), the visual palette, and contact addresses MUST be
  consistent across all pages and all languages.
- The Terms and Privacy pages MUST accurately describe what the app actually
  does, in every language. Translations of legal copy MUST preserve the legal
  meaning of the source; each legal page MUST state its "effective as of" date
  and which language version is governing/authoritative. Any change to data
  handling, third-party services, or contact details MUST be reflected in all
  language versions, and the effective date MUST be updated when their substance
  changes.

Rationale: This site is the authoritative public statement of what Billbird is
and how it treats user data; inaccurate or untranslated legal/privacy copy is
both a trust and a compliance risk. Serving the full site in the visitor's
language broadens reach while the Spanish-default fallback guarantees no visitor
is left with missing content.

## Technology & Hosting Constraints

- Stack: HTML5 + CSS only; optional vanilla JavaScript as a last resort (see
  Principle IV). No frameworks, package managers, or build tools.
- Hosting: GitHub Pages, published from `docs/` on the default branch.
- Assets: stored alongside the pages in `docs/`; referenced by relative path.
- Encoding & viewport: every page MUST declare `<meta charset="UTF-8">` and a
  responsive `<meta name="viewport">`.
- Responsiveness: layouts MUST render correctly on mobile and desktop widths.

## Development Workflow

- Changes are made directly to the static files; verify by opening the affected
  page(s) in a browser before merging.
- Pre-merge checklist: links and assets resolve via relative paths (II); markup
  is semantic and accessible (III); no tracking/analytics introduced (IV); brand,
  language, and legal copy remain consistent and accurate (V).
- Commits SHOULD be small and descriptive. Legal/privacy edits MUST be called
  out explicitly in the commit message.
- Because production is GitHub Pages, treat a merge to the default branch as a
  deploy: do not merge a page that you have not loaded successfully.

## Governance

This constitution supersedes ad-hoc practice for the Billbird Docs repository.
All changes to the site MUST comply with the Core Principles; any deviation MUST
be justified in the pull request or commit and recorded as an amendment if it
sets new precedent.

Amendments follow semantic versioning of this document:
- MAJOR: removal or redefinition of a principle, or a backward-incompatible
  governance change.
- MINOR: a new principle or materially expanded section.
- PATCH: clarifications, wording, or non-semantic refinements.

On amendment, update the version line below and the Sync Impact Report at the
top of this file, and review the `.specify/templates/*` artifacts for alignment.

**Version**: 1.1.0 | **Ratified**: 2026-06-15 | **Last Amended**: 2026-06-15
