# Contract: Language Switcher Component

A small, consistent control present on **every** content page (12 pages). Works with zero JavaScript.

## Markup contract

- A labeled group of three items, one per language, rendered as plain `<a>` links to the **same page key** in the other languages, plus a visually-marked **active** item for the current language.
- Example on `es/support.html`:
  - `Español` → active (current; rendered non-link or `aria-current="page"`).
  - `English` → `../en/support.html`.
  - `Português (BR)` → `../pt-br/support.html`.
- Display names are each language's own endonym: `Español`, `English`, `Português (BR)`.
- The control must appear in a consistent, discoverable location across pages (e.g. header/top of page and/or footer). Recommend top-of-page for marketing/support and header area for legal pages.

## Behavior contract

- **Switching**: clicking a link navigates to the sibling-language version of the current page (FR-008/FR-009) — no JS required.
- **Active indication**: the current language is visually distinct and exposed to assistive tech via `aria-current="page"` (FR-003, Principle III).
- **Persistence (progressive enhancement)**: on page load, each content page sets `localStorage['billbird-lang']` to its own language code so the root router restores it on return visits (FR-006). If JS is disabled, switching still works; only return-visit memory is lost.

## Accessibility contract (Principle III)

- Links are keyboard-focusable and operable; active state not conveyed by color alone (use weight/underline/`aria-current`).
- The group has an accessible name (e.g. `aria-label` "Language" / "Idioma" / "Idioma" localized, or a visible label).
- Sufficient contrast against the existing palette.

## Acceptance

- On each of the 12 pages, the switcher shows 3 languages with the correct one marked active.
- Clicking each non-active language lands on the same page in that language (e.g. `pt-br/terms.html` → `en/terms.html`).
- Keyboard: Tab reaches each link; Enter activates.
- With JS disabled, the switcher is fully functional (links work); with JS enabled, returning to `/` later restores the last language used.
