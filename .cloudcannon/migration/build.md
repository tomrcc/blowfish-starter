# Build and test

## Local results

| Check | Result |
| --- | --- |
| Clean build (`rm -rf public resources/_gen; hugo`) | 38 pages, 15 processed images, no warnings |
| Page parity vs audit baseline | Same 32 HTML files; `<img>` counts match (home 7, post 7, about 1) |
| Regions in output | Home: 5 text (caption, heading, intro, body) + 1 array + 1 image; each post/page: title + `@content` |
| Renderer assets | `public/_cloudcannon/hugo_renderer.wasm.<hash>.gz`, `hugo-worker.<hash>.js`, `live-editing.<hash>.js` |
| Bundle contents | `_vendor/.../layouts/partials/cta-button.html`, theme shortcodes, `i18n/`, project `layouts/partials/home/landing.html`, `config/_default/*.toml` |
| Editor-mode build (`hugo --config <tmp>/envclient.yaml`, which merges with `config/_default/`) | Builds; `<img>` counts unchanged. Processed images stay at 15 because Blowfish never checks `ENV_CLIENT` (expected: only re-rendered partials matter) |
| Fresh clone, empty `HUGO_CACHEDIR`, no Go on `PATH` | Builds identically from `_vendor/` (renderer WASM fetched from GitHub) |
| `npx @cloudcannon/cli validate` | Both files valid |

## For the user to check in CloudCannon (or `npx @cloudcannon/cli dev public`)

1. `/` and `/posts/` open in the Visual Editor (`_index.md` URL collapse).
2. Home: type in the caption, heading and intro; add, reorder and remove a button (the new button renders through `cta-button`, with its icon); replace the background image; add, reorder and remove sections, and edit a stat's figure and a card's title in place.
3. A post: edit the title and body in place; drop an image into the body in the Content Editor, and confirm it lands in the post's folder and renders after a rebuild.
4. Content Editor on "Shortcode showcase" and the home page: every snippet shows as a card (not "unknown"), edit one of each, save, and check the diff touches only that value. Especially check `badge` and `lead`.
5. Site settings: change the colour scheme and save; `params.yaml` keeps its layout (and add an author link from the structure menu).
6. Open an existing post and save without changes: no keys added (in particular, `draft` not flipped).
7. **Posts → Add → Post**: the new file is `content/posts/<slug>/index.md`, `date` is filled with now, `draft: true`; upload a featured image and confirm it lands in that folder. **Pages → Add → Page**: the new file is `content/<slug>.md`.
