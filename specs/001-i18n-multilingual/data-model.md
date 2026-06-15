# Phase 1 Data Model: Multilingual Site (i18n)

This is a static site with no database. The "data model" describes the content/configuration entities the implementation manipulates: languages, pages, the per-language string sets, and the stored preference.

## Entity: Supported Language (Locale)

A language the site is published in.

| Field | Value(s) | Notes |
|-------|----------|-------|
| `code` | `es`, `en`, `pt-br` | Used as the directory name and `<html lang>` value (`pt-br` → `lang="pt-BR"`). |
| `displayName` | `Español`, `English`, `Português (BR)` | Shown in the language switcher (each language's name in its own language). |
| `isDefault` | `true` for `es`, else `false` | Default + fallback (R2). |
| `browserMatch` | prefix rules | `en*`→en, `es*`→es, `pt*`→pt-br (R3). |
| `hreflang` | `es`, `en`, `pt-br` | Matches `code`; `x-default` maps to `es`. |

**Set is fixed** at three locales. Adding a locale = add a directory + its 4 pages + switcher entry + hreflang/sitemap entries.

## Entity: Page

A logical page that exists once per language.

| Field | Value(s) | Notes |
|-------|----------|-------|
| `key` | `index`, `support`, `terms`, `privacy` | Identifies the page across languages. |
| `path` | `<lang>/<key>.html` | e.g. `es/support.html`. `index` is the language home. |
| `type` | `marketing` (index), `support`, `legal` (terms, privacy) | `legal` pages carry effective date + governing-language notice (FR-012). |
| `translatableStrings` | see below | All user-facing text + metadata for this page. |

**Page set per language**: exactly the 4 keys above. 4 keys × 3 locales = 12 page instances. Plus non-localized infra files: root `index.html` (router), `404.html`, `styles.css`, `sitemap.xml`, `icon.png`.

## Entity: Translatable String Set

Per (page, language), the complete set of human-facing text. No string may be left in another language (FR-002, SC-001/SC-006). Categories that MUST be translated:

- `meta.title` — `<title>`
- `meta.description` — `<meta name="description">`
- `headings` — all `h1`–`h3`, section tags/eyebrows
- `body` — paragraphs, list items, card text
- `controls` — button labels, link/nav labels, footer links, language switcher labels
- `legal` (legal pages only) — full legal copy + `effectiveDate` + `governingLanguageNotice`

**Validation rules**:
- Completeness: every string present in the default (`es`) page exists in `en` and `pt-br` (no empties).
- Consistency: brand name "Billbird", palette, and contact address (`ilynchdev@gmail.com`) identical across all languages (Principle V).
- Language purity: declared `<html lang>` matches the content; no second language on the page (SC-006).
- Fallback: a missing string is filled from the `es` source rather than left blank (FR-011).

## Entity: Language Preference (client state)

Persisted visitor choice, used only by the root router.

| Field | Value | Notes |
|-------|-------|-------|
| storage | `localStorage` | Key `billbird-lang`. |
| value | one of `es` / `en` / `pt-br` | Written by each content page on load (R4); read by root router on entry (R3). |
| lifecycle | set on visiting any content page; read at root; never read by a server | Absent value → fall back to browser detection → default `es`. |

## Relationships

- One **Page** key ↔ many **Page instances** (one per **Language**).
- One **Page instance** ↔ one **Translatable String Set**.
- **Language Preference** references one **Language** `code`.
- **SEO metadata** (canonical/hreflang) links every Page instance to all its sibling-language instances (contract: `seo-metadata.md`).
