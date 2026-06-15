---
description: "Task list for Multilingual Site (i18n) — EN/ES/PT-BR"
---

# Tasks: Multilingual Site (i18n) — English, Spanish, Brazilian Portuguese

**Input**: Design documents from `/specs/001-i18n-multilingual/`

**Prerequisites**: plan.md, spec.md, research.md, data-model.md, contracts/, quickstart.md

**Tests**: No automated tests requested. This is a zero-build static site; verification is manual per `quickstart.md` (no test tasks generated).

**Organization**: Tasks are grouped by user story. Setup + Foundational establish the per-language structure and the Spanish reference set (default/fallback) that every story builds on.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story the task belongs to (US1, US2, US3)
- Exact file paths are included in every task

## Path Conventions

Static site published from `docs/` on GitHub Pages. Per-language content lives under `docs/es/`, `docs/en/`, `docs/pt-br/`. Shared assets (`styles.css`, `icon.png`, `sitemap.xml`, root `index.html`, `404.html`) live at `docs/`. Base URL for SEO: `https://ivanlynch.github.io/billbird-docs/` (see research R6).

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Create the directory layout and the shared stylesheet so all 12 pages differ only by translated text.

- [X] T001 Create language directories `docs/es/`, `docs/en/`, `docs/pt-br/`
- [X] T002 [P] Extract the shared/common CSS currently inlined in `docs/index.html` (and the per-page `<style>` blocks of support/terms/privacy) into a single `docs/styles.css`; keep only genuinely page-specific rules scoped inside it

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Produce the Spanish reference set (the default + fallback content and the translation source) and make the bare site root resolve. Every user story depends on these pages existing.

**⚠️ CRITICAL**: No user story work can begin until this phase is complete.

- [X] T003 [P] Migrate current home into `docs/es/index.html`: move the existing `docs/index.html` body, link to `../styles.css`, fix asset ref to `../icon.png`, keep internal links relative within `es/` (`support.html`, `terms.html`, `privacy.html`), confirm `<html lang="es">`
- [X] T004 [P] Migrate current support page into `docs/es/support.html`: same rewiring (`../styles.css`, `../icon.png`, relative `es/` links), `<html lang="es">`
- [X] T005 [P] Migrate current terms page into `docs/es/terms.html`: rewire to `../styles.css`/`../icon.png`, `<html lang="es">`, retain effective date `2026-03-22`, add a governing-language notice placeholder (per contracts/seo-metadata.md, FR-012)
- [X] T006 [P] Migrate current privacy page into `docs/es/privacy.html`: same rewiring, `<html lang="es">`, retain effective date `2026-03-22`, add governing-language notice placeholder
- [X] T007 Replace `docs/index.html` with an interim default redirect to `es/` using `<meta http-equiv="refresh" content="0; url=es/">` plus a `<noscript>` link to `es/`, so the bare root resolves to the default language with no JavaScript (depends on T003)

**Checkpoint**: Spanish site fully live under `/es/`; the bare root redirects to it; shared CSS in place.

---

## Phase 3: User Story 1 - Read the whole site in my own language (Priority: P1) 🎯 MVP

**Goal**: Every public page is fully available and readable in all three languages, with the correct `lang` and no mixed-language content.

**Independent Test**: For each language (`es`, `en`, `pt-br`) and page (`index`, `support`, `terms`, `privacy`), open `/<lang>/<page>.html` and confirm all headings, body, controls, footer, and the tab title are in that language, `<html lang>` matches, and no second language appears.

### Implementation for User Story 1

- [X] T008 [P] [US1] Create `docs/en/index.html` by translating `docs/es/index.html` to English: translate all text + `<title>` + `<meta name="description">`, set `<html lang="en">`, link `../styles.css`/`../icon.png`, relative `en/` internal links
- [X] T009 [P] [US1] Create `docs/en/support.html` (English translation of `docs/es/support.html`), `<html lang="en">`, translated metadata, relative `en/` links
- [X] T010 [P] [US1] Create `docs/en/terms.html` (English; this is the legal reference/governing version per R8), `<html lang="en">`, effective date `2026-03-22`, governing-language notice
- [X] T011 [P] [US1] Create `docs/en/privacy.html` (English), `<html lang="en">`, effective date `2026-03-22`, governing-language notice
- [X] T012 [P] [US1] Create `docs/pt-br/index.html` by translating to Brazilian Portuguese, set `<html lang="pt-BR">`, translated `<title>`/`<meta description>`, `../styles.css`/`../icon.png`, relative `pt-br/` links
- [X] T013 [P] [US1] Create `docs/pt-br/support.html` (Brazilian Portuguese), `<html lang="pt-BR">`, translated metadata, relative `pt-br/` links
- [X] T014 [P] [US1] Create `docs/pt-br/terms.html` (Brazilian Portuguese), `<html lang="pt-BR">`, effective date `2026-03-22`, governing-language notice
- [X] T015 [P] [US1] Create `docs/pt-br/privacy.html` (Brazilian Portuguese), `<html lang="pt-BR">`, effective date `2026-03-22`, governing-language notice
- [X] T016 [US1] Verify all 12 content pages against data-model.md completeness rules: correct `<html lang>`, fully translated text + metadata, no mixed-language content, brand/contact consistent (SC-001, SC-006)

**Checkpoint**: All 12 pages exist and are fully readable per language — MVP content complete.

---

## Phase 4: User Story 2 - Switch language and have it remembered (Priority: P2)

**Goal**: A visible language switcher on every page lets visitors change language (pure links, no JS) and their choice persists across navigation and return visits.

**Independent Test**: From any page, use the switcher to change language → lands on the same page in the new language with that language marked active; navigate between pages → stays in the chosen language; return to `/` → the last-chosen language is restored.

### Implementation for User Story 2

- [X] T017 [US2] Add language-switcher styles to `docs/styles.css` (group layout, active-state styling not relying on color alone, responsive on mobile)
- [X] T018 [P] [US2] Add the switcher to each `docs/es/*.html` page (`index`, `support`, `terms`, `privacy`) per contracts/language-switcher.md: links to `../en/<page>.html` and `../pt-br/<page>.html`, `aria-current="page"` on Spanish, plus the on-load `localStorage['billbird-lang']='es'` persistence snippet
- [X] T019 [P] [US2] Add the switcher to each `docs/en/*.html` page: links to `../es/<page>.html` and `../pt-br/<page>.html`, `aria-current` on English, persistence snippet `='en'`
- [X] T020 [P] [US2] Add the switcher to each `docs/pt-br/*.html` page: links to `../es/<page>.html` and `../en/<page>.html`, `aria-current` on Portuguese, persistence snippet `='pt-br'`
- [X] T021 [US2] Verify switching on all 12 pages (each non-active language lands on the same page key in that language) and persistence (after choosing a language and revisiting `/`, the choice is restored) — SC-002, SC-004

**Checkpoint**: Language switching works everywhere with zero JS; preference persists for return visits.

---

## Phase 5: User Story 3 - Land in the right language and share it (Priority: P3)

**Goal**: First-visit browser-language auto-detection at the root, plus SEO metadata and a sitemap so each language version is discoverable and shareable.

**Independent Test**: Clear `localStorage`, set the browser language → opening `/` lands on the matching language (unsupported → `es`); open a deep link (e.g. `/en/support.html`) → opens in that language; verify hreflang/canonical and sitemap.

### Implementation for User Story 3

- [X] T022 [US3] Upgrade `docs/index.html` into the full router per contracts/url-structure.md: synchronous `<head>` script resolving `localStorage['billbird-lang']` → `navigator.languages` prefix match (`en*`→en, `es*`→es, `pt*`→pt-br) → default `es`, then `location.replace('<lang>/')`; retain `<meta refresh>`→`es/` and `<noscript>` links to all three; add `<meta name="robots" content="noindex">`
- [X] T023 [P] [US3] Add `<link rel="canonical">` (self, absolute) + `hreflang` alternates for `es`/`en`/`pt-br`/`x-default` to each `docs/es/*.html` page (contracts/seo-metadata.md)
- [X] T024 [P] [US3] Add canonical + hreflang alternates to each `docs/en/*.html` page
- [X] T025 [P] [US3] Add canonical + hreflang alternates to each `docs/pt-br/*.html` page
- [X] T026 [P] [US3] Create `docs/sitemap.xml` listing all 12 absolute language URLs with `xhtml:link` hreflang alternates
- [X] T027 [P] [US3] Create `docs/404.html` linking to (and optionally redirecting to) the default language home
- [X] T028 [US3] Verify auto-detect (browser lang → correct dir; unsupported → `es`), deep-link sharing in a fresh profile, hreflang reciprocity across all 12 pages, sitemap validity, and root `noindex` (SC-003, SC-005, FR-010)

**Checkpoint**: First-time visitors auto-land in their language; every language version is shareable and discoverable.

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Quality, accessibility, and constitution compliance across all stories.

- [X] T029 [P] Accessibility pass across all pages: switcher keyboard order + `aria-current`, heading order, `alt` text, color contrast (Constitution III)
- [X] T030 [P] Responsive check (mobile + desktop) for the switcher and all pages
- [X] T031 Consistency audit across all 12 pages and 3 languages: brand "Billbird", contact `ilynchdev@gmail.com`, palette, legal effective date `2026-03-22`, and governing-language notice
- [X] T032 Constitution regression check: all files under `docs/`, all navigable links/assets use relative paths (only SEO metadata absolute), only the documented minimal JS present, no analytics/tracking added (Principles I, II, IV)
- [X] T033 Run all `quickstart.md` validation scenarios 1–8 and confirm they pass

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — start immediately.
- **Foundational (Phase 2)**: Depends on Setup. Produces the Spanish reference set — BLOCKS all user stories.
- **User Story 1 (Phase 3)**: Depends on Foundational (translates from `docs/es/`).
- **User Story 2 (Phase 4)**: Depends on US1 (switcher links need the EN/PT-BR pages to exist).
- **User Story 3 (Phase 5)**: Depends on US1 (router targets + hreflang need all language pages). Independent of US2.
- **Polish (Phase 6)**: Depends on all targeted stories being complete.

### Cross-Story File Note

US1, US2, and US3 all modify the same 12 content pages, in different phases. Run them **phase-by-phase** (not US2 and US3 simultaneously) to avoid edit conflicts on those files. Within a phase, the per-language `[P]` tasks touch different files and are safe to parallelize.

### Within Each Story

- US1: Spanish reference (Foundational) before EN/PT-BR; the 4 EN files are mutually parallel; the 4 PT-BR files are mutually parallel; EN and PT-BR groups are parallel to each other.
- US2: T017 (styles) can proceed in parallel with the page-edit tasks; verify (T021) last.
- US3: T022 (router) is independent of the per-page SEO tasks; verify (T028) last.

### Parallel Opportunities

- T001 and T002 (Setup).
- T003–T006 (Foundational Spanish migration) — different files.
- T008–T015 (all eight EN + PT-BR page translations) — different files.
- T018–T020 (switcher per language) — different files.
- T023–T027 (SEO metadata per language + sitemap + 404) — different files.
- T029, T030 (Polish a11y + responsive).

---

## Parallel Example: User Story 1

```bash
# After Foundational completes, build all eight non-Spanish pages in parallel:
Task: "Create docs/en/index.html (English translation)"
Task: "Create docs/en/support.html (English translation)"
Task: "Create docs/en/terms.html (English translation)"
Task: "Create docs/en/privacy.html (English translation)"
Task: "Create docs/pt-br/index.html (Brazilian Portuguese translation)"
Task: "Create docs/pt-br/support.html (Brazilian Portuguese translation)"
Task: "Create docs/pt-br/terms.html (Brazilian Portuguese translation)"
Task: "Create docs/pt-br/privacy.html (Brazilian Portuguese translation)"
```

---

## Implementation Strategy

### MVP First (Setup + Foundational + User Story 1)

1. Phase 1 Setup → 2. Phase 2 Foundational (Spanish live, root resolves) → 3. Phase 3 US1 (all 12 pages readable).
4. **STOP and VALIDATE**: open all 12 pages, confirm full translation and correct `lang`.
5. Deployable: a complete trilingual site reachable by direct/shared URL (default Spanish at root).

> Note: US1 is the content MVP. For a comfortable public launch, add **US2** (the switcher) so visitors can move between languages without editing URLs.

### Incremental Delivery

1. Setup + Foundational → Spanish baseline + structure.
2. US1 → all three languages readable (MVP) → deploy.
3. US2 → in-page language switching + persistence → deploy.
4. US3 → auto-detect landing + SEO/shareability → deploy.

---

## Notes

- [P] = different files, no dependencies; safe to parallelize within a phase.
- Switching itself requires no JavaScript; the only JS is the root auto-detect (US3) and the one-line persistence snippet (US2), both progressive enhancements with no-JS fallbacks.
- Keep all navigable links/assets relative; only SEO metadata (canonical/hreflang/sitemap) uses absolute URLs.
- If a custom domain is later added, update the base URL in canonical/hreflang/sitemap and add `docs/CNAME`.
- Commit after each task or logical group; call out legal-page edits explicitly (Constitution V).
