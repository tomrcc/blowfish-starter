# Configuration

## Decisions

- **Collections:**
  - `pages`: `content/*.md`, excluding `posts/**`, `url: /[full_slug]/`. Schemas are `default` (page) and `home`. `add_options` offers Page only, so the home page can't be duplicated.
  - `posts`: `content/posts`, `url: /posts/[full_slug]/`. `create.path` makes every new post a bundle. Schemas are `default` (post) and `list` (the posts `_index.md`, which is kept because its title, description and body drive the list header). New posts open in the Content Editor, because drafts aren't built.
  - `settings`: `config/_default/{params,languages.en,menus.en}.toml`, data editor only, file actions disabled. These are Blowfish's own config files, so the theme docs apply unchanged. `hugo.toml`, `markup.toml` and `module.toml` stay developer-only.
- **Site config as data:** the three editable files are **YAML** (see visual-editing.md: the editor rewrites collection files as YAML front matter, which breaks TOML config), and carry **no comments**. The explanations live in `_inputs` labels and comments, plus the README. **Why:** CloudCannon rewrites a data file when it saves, and comments may not survive (unverified). Developer-facing comments are in the non-editable files only.
- **Author links** (`params.author.links`) are Blowfish's array of single-key maps (`{ github = "…" }`). They're modelled as one structure value per network, each with a one-key `value`.
- **Images:**
  - Global `paths` point to `assets/img` with `static: assets`, because Blowfish loads author, logo, home and hero images with `resources.Get`. Stored values look like `img/x.jpg`.
  - Post bodies and `featureimage` upload into the bundle through `_editables.content.paths` and the input's `options.paths` (`uploads_use_relative_path`). A collection-level `paths` fails validation.
- **Markdown:** `html: true` (Goldmark `unsafe`), `table`, `strikethrough` and `linkify` on, `typographer` off. Goldmark block attributes are on, but the content doesn't use them; CloudCannon `attributes` stays off.
- **Snippets:**
  - Blowfish's own `alert`, `button`, `badge`, `lead`, `figure` and `youtubeLite` use Hugo templates.
  - No `_snippets_imports`: Blowfish overrides Hugo's built-in `figure`, so `hugo_figure` would describe the wrong arguments.
- **Timezone:** `Etc/UTC`, matching Hugo's default. The README tells site owners to change it alongside Hugo's `timeZone`.
- **Build:** `hugo` (not the CLI's `hugo -b /`), `hugo_version: 0.166.0`, CLI cache env and preserved paths kept.

## To verify in the editor (Phase 5)

- `_index.md` URL collapse for `/` and `/posts/`.
- Snippets round-trip, especially the no-argument paired `badge`/`lead`.
- Upload into a post bundle: that `[relative_base_path]` resolves to the post's folder.
- Saving a settings file: key order preserved (a TOML save was seen to strip indentation and blank lines; comments untested).
