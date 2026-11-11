# Lumen Design System

A reusable CSS/SCSS design system and component library with design tokens, BEM components, cascade layers, container queries and light/dark theming.

## Quick start

npm i -g sass
sass --no-source-map scss/main.scss css/ds.css   # add --watch while developing


Open docs/index.html in a browser (it loads '../css/ds.css'). To use the system in another project, link 'css/ds.css' and add the theme script from the docs page.

## Project structure

lumen-design-system/
├── scss/
│   ├── main.scss              # entry point: layer order + imports
│   ├── abstracts/             # no CSS output
│   │   ├── _tokens.scss       # maps, rem(), mixins
│   │   └── _index.scss        # @forward 'tokens'
│   ├── themes/_themes.scss    # token + light/dark custom properties
│   ├── base/_base.scss        # reset, typography
│   ├── layout/_layout.scss    # container, grid, stack, cluster, .cq
│   ├── components/            # button, form, card, feedback
│   └── utilities/_utilities.scss
├── css/ds.css                 # compiled output
└── docs/index.html            # component documentation page

## Architecture

Cascade layers (declared in `main.scss`, later layers win regardless of specificity):

`reset` → `base` → `layout` → `components` → `utilities`

Each partial is loaded into its layer with `meta.load-css`, so components never need `!important` or high specificity to override base styles. Utilities are the only place `!important` is used, on purpose.

Naming: BEM, `block__element--modifier` (e.g. `.card__title`, `.btn--outline`). State classes use `is-` (e.g. `.is-invalid`).

Sass vs CSS variables: Sass maps and variables exist at compile time and generate the CSS custom properties. Custom properties exist at runtime, which is what makes theme switching possible without recompiling.

## Design tokens

| Group | Variables |
| Colour | `--color-{primary, secondary, accent, success, warning, danger, info, bg, surface, text, muted, border, on-fill}` |
| Spacing | `--space-1` … `--space-7` (0.25rem – 3rem) |
| Type scale | `--text-xs` … `--text-3xl` (1.25 ratio, 0.75rem – 2.441rem) |
| Radius | `--radius-{sm, md, lg, pill}` |
| Shadow | `--shadow-{sm, md, lg}` |
| Fonts | `--font-body`, `--font-head` |
| Layout | `--container` |

Breakpoints (`sm` 40rem, `md` 48rem, `lg` 64rem) live in the Sass map `$breakpoints` and are used through the `bp()` mixin.

## SCSS toolkit

| Item | Purpose |
| `theme-vars($map)` | Emits a colour map as `--color-*` properties |
| `bp($name)` | Min-width media query from `$breakpoints` |
| `focus-ring` | Consistent `:focus-visible` outline |
| `rem($px)` | Converts pixels to rem |
| `@each` loops | Generate tokens, status button colours, alert variants, spacing utilities |

## Components

| Component | Classes |
| Button | `.btn` + `--primary --secondary --accent --success --warning --danger --info`, `--outline --ghost`, `--sm --lg`, `--loading`, `disabled` |
| Form | `.field` (`__label`, `__hint`), `.input` (`--textarea`, `.is-valid`, `.is-invalid`), `.check`, `.switch` (`__input`) |
| Card | `.card` (`__title`, `__meta`, `__value`, `__price`) + `--stat --pricing --featured`; `.profile` (`__avatar`) |
| Alert | `.alert` + `--success --warning --danger --info` |
| Badge | `.badge` + `--success --info` |
| Progress | `.progress` > `.progress__bar`, set with `style="--value:65%"` |
| Tooltip | `.tooltip` with `data-tip="text"` |
| Modal | `.modal` on a `<dialog>`, opened with `showModal()` |

Typography uses element styles (`h1`–`h4`, `p`, `a`, `blockquote`, `code`) plus `.label`, `.caption` and `.text-muted`.

Layout helpers: `.container`, `.grid`, `.stack`, `.cluster`, `.cq`.

## Theming

Priority order:

1. `data-theme="light|dark"` on `<html>`, set by the toggle and saved in `localStorage` under the key `theme`.
2. If nothing is saved, the system `prefers-color-scheme` applies automatically through CSS.

Adding a theme means adding one map in `_tokens.scss` and one `theme-vars` call in `_themes.scss`. Components do not change.

## Responsive behaviour

- Container queries (`@container`) are used for components that move between layouts, such as `.profile`. Wrap the component in an element with `.cq`. It then responds to its own width, wherever it is placed.
- Media queries are better for page-level concerns: navigation, grid column counts, and `prefers-color-scheme` / `prefers-reduced-motion`.
- `@supports` guards `color-mix()` in alerts, and `@starting-style` gives the modal its entry animation where supported.

## Testing checklist

- [ ] 375px, 768px and 1280px wide, light theme
- [ ] 375px, 768px and 1280px wide, dark theme
- [ ] Theme choice survives a page reload
- [ ] With storage cleared, the page follows the system theme
- [ ] Keyboard focus is visible on buttons, inputs, switch and tooltip
- [ ] Modal opens and closes, and the profile card re-flows when resized

## Maintainability rules

- Take values from tokens. No raw hex colours or pixel sizes inside components.
- Keep Sass nesting to two levels, and use `&` for BEM elements and modifiers.
- Do not use `!important` outside the `utilities` layer.
- Add each new component as its own partial under `components/`, then register it in `main.scss`.