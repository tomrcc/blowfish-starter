# Content

- Every content file sets `_schema`: `home` (`content/_index.md`), `default` (pages and posts), `list` (`content/posts/_index.md`).
- Every post carries the full post schema key set (`series`, `series_order`, `featureimage`, `featureimagecaption`, `heroStyle` are empty where unused), so the sidebar is the same on every post. Empty values fall through to the site settings in Blowfish's templates.
- Published posts set `draft: false` explicitly (the schema default is `true`, for new posts).
- Every `heroButtons` item carries every structure key (`label`, `url`, `style`).
- No structural changes: bundles and `_index.md` files kept.

Parity with the audit baseline: same 32 HTML files; `<img>` counts match on home, a post and about.
