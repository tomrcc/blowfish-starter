# Audit

Starter built from scratch on Blowfish; there is no pre-existing site. The audit covers the scaffold as written in step 0.

## 1. Hugo version, modules and themes

| Item | Value |
| --- | --- |
| Hugo | 0.166.0 extended (`regression-tests/.bin/hugo`). Brew Hugo 0.148.2 fails with a template error (`can't evaluate field Locale in type *langs.Language`) after only a WARN about the version range. |
| Theme | `github.com/nunocoracao/blowfish/v3` v3.8.0 as a Hugo module (module cache) |
| Theme Hugo range | `config.toml` `[module.hugoVersion]` extended, min 0.163.0, max 0.166.0. `theme.toml` has no `min_version`. |
| Target `hugo_version` | 0.166.0: inside Blowfish's window and ≥ 0.150 for editable regions |
| Go | 1.19.3 locally. Needed for `hugo mod get`; not needed at build time once `_vendor/` is committed (verify in Phase 5). |
| `go.mod` | Yes (`github.com/CloudCannon/blowfish-starter`) |
| Templates come from | Module cache → **must vendor** in Phase 4. The project has no `layouts/` yet. |
| Theme layout style | Classic: `layouts/_default/`, `layouts/partials/`, `layouts/shortcodes/`, `_default/_markup/`. Project overrides go in the same classic folders. |
| Bookshop | None |
| npm tooling | None. Blowfish ships precompiled Tailwind (`assets/css/compiled/main.css`). |

Parity baseline: `$SCRATCHPAD/blowfish-baseline` (38 pages).

## 2. Content sections

| Section | Files | Layout | `_index.md` | Notes |
| --- | --- | --- | --- | --- |
| (root) | `_index.md`, `about.md` | home: theme `index.html` → `partials/home/landing.html`; about: `_default/single.html` | `_index.md` drives the home page (landing front matter + body) | |
| `posts` | 6 leaf bundles + `_index.md` | `_default/single.html`, `_default/list.html` | Title, description and body drive the list header | Bundles hold `featured.jpg` and inline images |

Front matter is all YAML.

| Field | Type | Where | Read by |
| --- | --- | --- | --- |
| `title` | string | all | everywhere |
| `description` | string | all | meta, cards, list summaries |
| `date` | date | posts | article meta, sorting |
| `draft` | bool | posts | Hugo |
| `tags`, `categories`, `series` | string[] | posts | taxonomies; series box |
| `series_order` | number | series posts | `partials/series/*` |
| `heroCaption`, `heroLead` | string (markdown) | `_index.md` | `home/landing.html` (`markdownify`) |
| `heroButtons` | `{label, url, style?, github?}[]` | `_index.md` | `home/landing.html` → `cta-button.html` |
| `heroImage` | asset path | `_index.md` | `home/landing.html` (`resources.Get`) |
| `showDate`, `showReadingTime`, `showWordCount`, `showAuthor`, `showTableOfContents` | bool | about | `single.html`, `article-meta` |
| optional Blowfish per-page params | bool/string | any | front matter → `site.Params.article.*` → default |

There are no routing overrides and no `permalinks`. Taxonomies are `tags`, `categories` and `series`, with free-form values. There is no `data/` directory. Authors come from `params.author` in `languages.en.toml`, not `data/authors`.

## 3. Pages and routing

| Page | Source | Kind |
| --- | --- | --- |
| `/` | `content/_index.md` | home, theme `landing` layout + recent articles |
| `/about/` | `content/about.md` | page |
| `/posts/` | `content/posts/_index.md` | section list (paginated, 20) |
| `/posts/<slug>/` | leaf bundles | single |
| `/tags/…`, `/categories/…`, `/series/…` | generated | taxonomy/term |
| `/404.html` | theme `404.html` | layout-only; strings come from theme `i18n/` |
| `/index.json`, `/index.xml`, `sitemap.xml`, `llms.txt` | generated | outputs |

## 4. Layouts and partials

All templates come from the theme. The audit script's layout scans were empty; they were rerun by hand on the module dir from `hugo config mounts`.

- **Base:** `_default/baseof.html` → `head.html` (hooks `extend-head.html` (cached, gets `.Site`) and `extend-head-uncached.html` (gets page)), `header/<layout>.html`, `footer.html` (hook `extend-footer.html`).
- **Home:** `index.html` (page template) → `partials/home/landing.html` → `partials/cta-button.html` per button. `landing.html` renders `.Content` and calls `resources.Get`/`GetRemote` + `Minify`/`Fingerprint` for the hero.
- **Single:** `_default/single.html`. `<h1>{{ .Title }}` and `{{ .Content }}` are **inline in the page template**, not in partials. Hero via `partials/hero/<heroStyle>.html` (asset pipeline).
- **List:** `_default/list.html`. `<h1>` and `.Content` inline.
- **Asset pipeline:** 47 layout files, heaviest in `head.html`, `vendor.html`, `home/*`, `hero/*`, `article-link/*`, `figure`, `render-image`. `partials/icon.html` reads SVGs with `resources.Get`.
- **`.Content` in partials:** `home/*.html` render the page body. `icon.html`, `head.html`, `cta-button.html` and `header/basic.html` match only because they read `.Content` on a **resource** (SVG text, fetched JSON). Those are false positives.
- **Shortcodes used:** stats/stat, feature-grid/feature, alert, lead, button, badge, figure, youtubeLite. The script double-counts paired shortcodes; real usage is half for the paired ones.

### Census

| Page / section | Treatment | Needs editable region? |
| --- | --- | --- |
| Home hero: caption, title, lead | Keep theme `landing` model; primitive text regions via project override of `partials/home/landing.html` | Yes |
| Home hero buttons | Array region over `heroButtons`; items re-render `cta-button.html` (theme partial, bundled via `_vendor`) | Yes |
| Home hero image | Image region | Yes (check the editor with a `resources.Get` guard) |
| Home body (stats, feature grid shortcodes) | `@content` region; shortcodes as snippets | Yes |
| Home recent articles | Generated from posts | No: generated list |
| Post/page title | Primitive text region: needs override of `_default/single.html` | Yes |
| Post/page body | `@content` | Yes |
| Post hero / featured image | Bundle resource by file name; sidebar `featureimage` | No: file-name convention, sidebar |
| Post meta, taxonomies, series box, author block | Generated or site settings | No: sidebar-only |
| Posts list header (title/body) | `_index.md` in posts collection | No: sidebar. Wiring it would mean forking `list.html` as well; see the decision in configuration.md |
| Header menus, footer, author profile, colours | `config/_default/{params,languages.en,menus.en}.toml` as a settings collection | No: sidebar/data editor |
| 404 | Theme i18n strings | No: theme-owned |

The skills' usual outcome ("home → page builder") is **not** used. The user chose to keep Blowfish's `landing` model so the theme's docs and updates keep working.

## 5. Build pipeline

Plain `hugo` with no npm and no post-build steps. The CLI suggested `hugo -b /`; we use `hugo` (baseURL comes from config).

## 6. Flags

- **Positional CSS:** 39 `:first-child`/`:last-child`/`:nth-child` rules in the compiled CSS (Tailwind `first:`/`last:` utilities and prose). The planned array region (`heroButtons`) is a flex row of `<a>`, so positional selectors don't matter there.
- **Global JS (`DOMContentLoaded`):** appearance, code copy, menu a11y, zen mode, tabs, toc, chart, typeit. None are in the regions we'll re-render.
- **Inline `<script>` in partials:** 29 files. `landing.html` emits a `<script src=hero-scroll-fade.js>` behind the hero image.
- **Entrance animations:** `landing-rise*` classes on the hero elements (CSS animation). Check they don't hide content in the editor.
- **Numbers as text:** `stat value="6"` is a shortcode param, fine. `series_order` is a number but sidebar-only.
- **Goldmark:** `unsafe = true`, block attributes, passthrough math.
- **Existing CMS config:** none.

## 7. Sectioning

About 9 content pages, 0 page-builder conversions, 3 collections. No thresholds tripped.
