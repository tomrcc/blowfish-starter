# Visual editing

## Setup

- Hugo 0.166.0 locally and in `hugo_version` (Blowfish's window is 0.163–0.166; editable regions needs ≥ 0.150; the editor's renderer is 0.164).
- `github.com/CloudCannon/editable-regions@v0.0.21` imported in `config/_default/module.toml` after Blowfish.
- Head include: `layouts/partials/extend-head.html` is Blowfish's documented hook (`partialCached`, gets `.Site`). The module partial is context-agnostic, so the hook serves every page without overriding `head.html`.
- Templates come from the module cache, so `hugo mod vendor` puts Blowfish and editable-regions in `_vendor/`, which is committed.

## Section census

| Page | Section | Source (template) | Partial? | Treatment | Binding plan |
| --- | --- | --- | --- | --- | --- |
| Home | Hero caption | `partials/home/landing.html` (override) | yes | primitive `text` (`data-type="text"`, rendered with `markdownify`) | `heroCaption` |
| Home | Hero heading | same | yes | primitive `text` span | `title` |
| Home | Hero intro | same | yes | primitive `text` (`data-type="text"`, `markdownify`) | `heroLead` |
| Home | Hero buttons | same → `partials/cta-button.html` (theme, vendored) | yes | `array`, items re-render `cta-button` (fallback `data-component` on the wrapper) | `heroButtons` |
| Home | Hero background image | same | yes | primitive `image` on the `<img>` | `heroImage` |
| Home | Sections (stats, feature grid) | dispatcher in `home/landing.html` → `partials/blocks/*.html` | yes | page builder: `array` over `content_blocks`, items re-render `blocks/<name>`; nested `array` + `text` primitives inside | `content_blocks` |
| Home | Body | same | yes | `@content` text region | `@content` |
| Home | Recent posts | `partials/recent-articles/*` | yes | sidebar-only: generated from posts | — |
| Post / page | Title | `_default/single.html` (override) | no (page template) | primitive `text` span | `title` |
| Post / page | Body | same | no | `@content` text region on a wrapper around `.Content` only | `@content` |
| Post | Hero / featured image | `partials/hero/big.html` | yes | sidebar-only: found by file name (`featured.jpg`) or `featureimage`; an image region would need a fork of every hero partial | `featureimage` (sidebar) |
| Post | Meta, author block, series box, taxonomies, related | theme partials | yes | sidebar-only: generated or site settings | — |
| Posts list | Title, intro | `_default/list.html` | no | sidebar-only: title/description in the sidebar; wiring would fork a second 130-line page template for a page most starters leave as-is | — |
| All | Header logo/title, menu | `partials/header/*` | yes | sidebar-only: Site settings (menus, languages) | — |
| All | Footer copyright, menu | `partials/footer.html` | yes | sidebar-only: Site settings | — |
| 404, taxonomy pages | — | theme | — | theme-owned; i18n strings | — |

## Overridden theme files

Each override is a copy of the Blowfish v3.8.0 file with region attributes added and a header comment saying what changed. Keep the list short: every file here must be re-synced after a Blowfish upgrade.

| File | Change |
| --- | --- |
| `layouts/partials/home/landing.html` | Regions on the caption, heading, intro, buttons, image and body; buttons pass the item itself to `cta-button.html` |
| `layouts/_default/single.html` | Regions on the title and a wrapper around `.Content` |
| `layouts/partials/icon.html` | Falls back to `readFile` on the theme's SVGs (exposed with `params.editable_regions.additional_dirs`), because the editor's Hugo has no `assets/` |
| `layouts/partials/extend-head.html` | New (a theme hook, not an override): loads editable regions |
| `layouts/partials/blocks/{stats,feature-grid}.html` | New: page-builder blocks copying the markup of Blowfish's `stats`/`feature-grid` shortcodes |

## Changes after the first editor test

- **Settings files are YAML.** In the editor, every hero button showed `failed to unmarshal config for path "config/_default/languages.en.toml": "_stream.toml:1:4"`. `editable-regions` `integrations/hugo/browser/index.ts` (~L87–91) writes **every collection file** into the in-browser Hugo as `---\n<json>\n---\n` at its own path. For the `settings` collection that replaced the TOML config with YAML front matter, so Hugo's config load failed, and so did every component render. As YAML, the rewritten file is still valid config (a JSON document, then an empty one). Hugo 0.166 parses it (tested locally), and settings edits now reach the editor's renderer live.
- **Stats and feature grid moved from body shortcodes to `content_blocks`.** In the Visual Editor, snippets inside `@content` show as snippet cards, not rendered markup. As page-builder blocks they render as they do on the site, with text editable in place and sections addable, removable and reorderable.
- **Icons:** the `icon.html` override plus `additional_dirs` keep icons in re-rendered buttons and cards.
