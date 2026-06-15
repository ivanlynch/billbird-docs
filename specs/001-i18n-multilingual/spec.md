# Feature Specification: Multilingual Site (i18n) — English, Spanish, Brazilian Portuguese

**Feature Branch**: `001-i18n-multilingual`

**Created**: 2026-06-15

**Status**: Draft

**Input**: User description: "la pagina tiene que tener internacionalizacion para Ingles, Español y Portugues de Brasil."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Read the whole site in my own language (Priority: P1)

A visitor opens any public page of the Billbird site (home/marketing, support, terms, privacy) and is able to read all of its content — headings, body text, buttons, navigation, and page titles — in one of the three supported languages: English, Spanish, or Brazilian Portuguese. No part of the page is left in a different language.

**Why this priority**: This is the core of the feature. A multilingual site that only partially translates a page (e.g. body translated but buttons or the support page still in another language) fails its purpose and damages trust. Delivering one fully translated language set across all pages is the minimum viable product.

**Independent Test**: Pick a supported language, open each public page in that language, and confirm every visible string and the page title/description are in that language with no mixed-language content and the correct `lang` declared.

**Acceptance Scenarios**:

1. **Given** a visitor viewing the home page in Spanish, **When** they read the page, **Then** all headings, paragraphs, buttons, navigation, and the browser tab title are in Spanish.
2. **Given** a visitor viewing the support page in Brazilian Portuguese, **When** they read the page, **Then** all content is in Brazilian Portuguese with no untranslated strings.
3. **Given** a visitor viewing the terms page in English, **When** they read the page, **Then** the legal content is presented in English and the page declares the matching language.

### User Story 2 - Switch language and have it remembered (Priority: P2)

A visitor can change the site language at any time using a clearly visible language selector that appears on every page. After choosing a language, the rest of their browsing (navigating between pages, and returning later) stays in that language until they change it again.

**Why this priority**: Discovery and control. Even with auto-detection, visitors need an obvious way to override the language and to keep their choice. Without persistence, the experience feels broken as users move between pages.

**Independent Test**: From any page, open the language selector, choose a different supported language, confirm the page switches fully to it, navigate to another page, and confirm it remains in the chosen language; reload/return and confirm the choice is retained.

**Acceptance Scenarios**:

1. **Given** a visitor on the home page in Spanish, **When** they select English from the language selector, **Then** the page content switches entirely to English and the selector indicates English as active.
2. **Given** a visitor who selected Brazilian Portuguese, **When** they navigate from the home page to the support page, **Then** the support page is shown in Brazilian Portuguese.
3. **Given** a returning visitor who previously selected English, **When** they reopen the site, **Then** the site is presented in English without further action.

### User Story 3 - Land in the right language and share it (Priority: P3)

On a first visit, the site presents content in the visitor's preferred browser language when it is one of the three supported languages; otherwise it falls back to the default language. Each language version of a page can be linked/shared so that a recipient opens the page in the intended language, and search engines can discover each language version.

**Why this priority**: Improves first-impression relevance and reach (shareability and search visibility), but the site is still usable and complete without it because Stories 1 and 2 already let any visitor reach any language manually.

**Independent Test**: Set the browser preferred language to each supported language and to an unsupported one, open the site fresh each time, and confirm the presented language matches expectations; share/open a language-specific link and confirm it opens in that language.

**Acceptance Scenarios**:

1. **Given** a first-time visitor whose browser prefers Portuguese (Brazil), **When** they open the site, **Then** the site is presented in Brazilian Portuguese.
2. **Given** a first-time visitor whose browser prefers a language not supported (e.g. French), **When** they open the site, **Then** the site is presented in the default language (Spanish).
3. **Given** a link to the English version of the support page, **When** another person opens it, **Then** the support page is shown in English.

### Edge Cases

- **Unsupported browser language**: visitor's preferred language is none of the three → site falls back to the default language (Spanish).
- **Generic Portuguese**: browser preference is `pt` / `pt-PT` rather than `pt-BR` → treated as Brazilian Portuguese (the only supported Portuguese variant).
- **JavaScript disabled**: per the site's no-/minimal-JavaScript posture, each language version MUST still be reachable and fully readable without scripting (e.g. via distinct per-language URLs), and at minimum the default language is served.
- **Missing/late translation for a specific string**: the site falls back to the default-language text for that string rather than showing empty or broken content; no page should be partially blank.
- **Deep link in a specific language**: opening a shared language-specific link presents that page in the intended language regardless of any prior preference.
- **Legal accuracy across languages**: terms/privacy translations must not change legal meaning; the governing language and effective date must remain clear.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The site MUST present every public page (home/marketing, support, terms, privacy) in all three supported languages: English, Spanish, and Brazilian Portuguese.
- **FR-002**: Every user-facing element MUST be translated for each language, including headings, body copy, buttons, links/navigation labels, and page metadata (browser tab title and meta description). No page may display mixed-language content.
- **FR-003**: A language selector MUST be visible and reachable on every page, allowing the visitor to switch between the three supported languages, and MUST visually indicate the currently active language.
- **FR-004**: On a visitor's first visit, the site MUST present content in the browser's preferred language when it matches a supported language (treating any Portuguese preference as Brazilian Portuguese); otherwise it MUST present the default language.
- **FR-005**: The default and fallback language MUST be Spanish.
- **FR-006**: A visitor's selected language MUST persist across page-to-page navigation and across return visits until they choose a different language.
- **FR-007**: Each page MUST declare a language indicator that matches the language of the content actually displayed.
- **FR-008**: Internal navigation MUST preserve the visitor's selected language (moving between pages does not reset the language).
- **FR-009**: Each language version of each page MUST be independently linkable/shareable so that opening such a link presents the page in that language.
- **FR-010**: Search engines MUST be able to discover and associate the alternate language versions of each page so that the appropriate language version can be surfaced.
- **FR-011**: When a translation for a specific string is unavailable, the site MUST fall back to the default-language text for that string rather than display empty or broken content.
- **FR-012**: Terms and privacy translations MUST preserve the legal meaning of the source content, and each legal page MUST state its effective date and which language version is authoritative/governing.

### Key Entities *(include if feature involves data)*

- **Supported Language (Locale)**: One of English, Spanish, Brazilian Portuguese. Has a code, a display name shown in the selector, and a flag indicating whether it is the default. Spanish is the default.
- **Translatable Page**: A public page (home, support, terms, privacy) that exists in each supported language. Each has a per-language set of strings (titles, metadata, headings, body, buttons, navigation labels) and a per-language addressable location.
- **Language Preference**: The visitor's currently chosen language, derived initially from browser preference and then from explicit selection, retained across navigation and return visits.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 100% of user-facing strings (including page titles and meta descriptions) on all four public pages are available and displayed in each of the three languages, with zero untranslated or mixed-language strings.
- **SC-002**: A visitor can switch to any supported language from any page and see the page fully rendered in that language effectively instantly (no perceptible reload delay, under 1 second).
- **SC-003**: A first-time visitor whose browser preferred language is one of the three supported languages is shown that language automatically, without any manual selection.
- **SC-004**: A visitor's chosen language is retained on 100% of subsequent page navigations and on return visits, with no unexpected reversion to another language.
- **SC-005**: Each of the four pages is independently reachable in all three languages, and opening a language-specific link presents that exact language 100% of the time.
- **SC-006**: Every page passes a language-consistency check: the declared page language matches the displayed content and no second language appears on the page.
- **SC-007**: All language versions remain fully readable and navigable with JavaScript disabled (at least the default language served and all languages reachable).

## Assumptions

- **Scope of pages**: The feature covers the four existing public pages (`index`/home, `support`, `terms`, `privacy`). No new pages, blog, or app UI are in scope.
- **Scope of content**: Translation covers all visible text plus user-facing metadata (page title, meta description). It does not include the product app itself, screenshots, or imagery text.
- **Default language**: Spanish is the default and fallback, reflecting the product's current primary market (Spanish-speaking) while adding Brazilian Portuguese and English reach.
- **Portuguese variant**: Only Brazilian Portuguese (pt-BR) is supported; any other Portuguese preference is mapped to it.
- **Translations are human-reviewed**: This spec covers the presence, completeness, selection, and persistence of translations and the mechanism for serving them — not an ongoing translation-authoring or machine-translation workflow. Actual translated copy is provided/reviewed by a human.
- **Legal review**: Terms and privacy translations are treated as informational unless legal counsel states otherwise; the page declares the governing language. This reflects that translating legal copy changes the site's current English-only legal posture and must be done deliberately.
- **Static hosting constraint**: The site remains a static, zero-build site served from `docs/` on GitHub Pages with no server-side runtime; any language detection/persistence must work within that constraint and honor the site's preference for no/minimal JavaScript. (Implementation approach is deferred to planning.)
- **Existing assets**: Shared assets (icon, fonts, styles) are reused across all language versions; only text content differs by language.
