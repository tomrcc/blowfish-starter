# Blowfish starter for CloudCannon

A starter blog built on [Blowfish](https://blowfish.page), a Hugo theme, and set up for editing in [CloudCannon](https://cloudcannon.com). It comes with placeholder content: a home page, an About page and a handful of posts that explain how everything works.

Editors can:

- edit the home page's heading, intro, buttons and background image in the **Visual Editor**, and add, remove and reorder its sections (stats and feature grids)
- edit the title and body of posts and pages in place, or in the **Content Editor**
- insert Blowfish shortcodes (alerts, buttons, badges, lead paragraphs, figures, YouTube) from the toolbar as **snippets**
- change the theme's colour scheme, header style, home page options, author profile and menus in **Site settings**, without touching code

The theme is used as-is, as a Hugo module. Three of its templates are overridden to add editing hooks, and the starter adds two small templates of its own for the home page sections (see [How the CloudCannon integration works](#how-the-cloudcannon-integration-works)).

## Quick start

### On CloudCannon

1. Put this folder in a Git repository (GitHub, GitLab or Bitbucket) and push it.
2. In CloudCannon, create a site from that repository.
3. CloudCannon reads the build settings from `.cloudcannon/initial-site-settings.json`: build command `hugo`, output `public`, Hugo 0.166.0.
4. Open the site. The dashboard shows the editor guide from `.cloudcannon/README.md`.

### Locally

You need **Hugo extended**, a version between **0.163.0 and 0.166.0**. Blowfish only supports a recent, narrow range (see [Updating Blowfish](#updating-blowfish)). Check yours with `hugo version`.

```sh
hugo server     # preview at http://localhost:1313
hugo            # build into public/
```

You don't need Go to build or preview. The theme is committed in `_vendor/`. You need Go only to [update the theme](#updating-blowfish).

If Hugo prints `WARN … is not compatible with this Hugo version` and then a template error such as `can't evaluate field Locale`, your Hugo is outside Blowfish's supported range.

To try the CloudCannon editor locally, build the site and then run the CloudCannon dev server:

```sh
hugo && npx @cloudcannon/cli dev public
```

Then open http://localhost:10101. Saves are written to your files. Rebuild with `hugo` to see them in the preview.

## Project structure

```
.
├── config/_default/         Hugo and Blowfish configuration (one file per topic)
│   ├── hugo.toml              core Hugo settings                     (developers)
│   ├── markup.toml            Markdown rendering                     (developers)
│   ├── module.toml            theme and editable-regions imports     (developers)
│   ├── params.yaml            Blowfish theme options                 (editable in CloudCannon)
│   ├── languages.en.yaml      site title, description, author        (editable in CloudCannon)
│   └── menus.en.yaml          header and footer menus                (editable in CloudCannon)
├── data/                    tags.yaml, categories.yaml, series.yaml:
│                              the lists offered in a post's taxonomy fields (editable in CloudCannon)
├── content/
│   ├── _index.md              the home page
│   ├── about.md               a standalone page
│   └── posts/
│       ├── _index.md          the Posts list page
│       └── <post-name>/       one folder per post
│           ├── index.md         the post
│           └── featured.jpg     its thumbnail, hero and sharing image
├── assets/
│   ├── img/                   logo, author photo, home page image
│   └── css/custom.css         your CSS, loaded after Blowfish's
├── layouts/                   the few templates this starter adds or overrides
├── archetypes/posts.md        template for `hugo new content posts/<name>/index.md`
├── _vendor/                   the Blowfish theme and CloudCannon's editable-regions module (don't edit)
├── cloudcannon.config.yml     how CloudCannon presents the content
└── .cloudcannon/
    ├── initial-site-settings.json   build settings for a new CloudCannon site
    ├── schemas/                     templates for new pages and posts
    └── README.md                    the editor guide shown on the CloudCannon dashboard
```

## Using Blowfish

Blowfish's [documentation](https://blowfish.page/docs/) applies to this starter as written. Here is how its main ideas map onto this project.

### Configuration

Blowfish splits its configuration across `config/_default/`, and this starter keeps that split. When the docs say "set `colorScheme` in `params.toml`", that's `config/_default/params.yaml` here.

**The three files editors can change are YAML, not TOML.** Blowfish's docs show TOML, but the options are the same: `colorScheme = "fire"` in the docs is `colorScheme: fire` here, and `[homepage]` with `layout = "landing"` is `homepage:` with `layout: landing` indented beneath it. They have to be YAML because CloudCannon's Visual Editor hands the files in the Site settings collection to its in-browser Hugo as YAML. A TOML file there fails to load, and every part of the page the editor re-renders shows an error. The developer-only files (`hugo.toml`, `markup.toml`, `module.toml`) stay TOML.

`params.yaml` here contains only the options the starter uses. To use another option from the [theme parameters reference](https://blowfish.page/docs/configuration/#theme-parameters), add it to `params.yaml`. To let editors change it too, add an input for it in `cloudcannon.config.yml` (see [Letting editors change a new option](#letting-editors-change-a-new-option)).

The three files editors can change (`params.yaml`, `languages.en.yaml`, `menus.en.yaml`) contain **no comments**. CloudCannon rewrites these files when an editor saves, so notes are kept here and in the CloudCannon field labels instead. Comments in `hugo.toml`, `markup.toml` and `module.toml` are safe, because editors never see those files.

### The home page

The home page uses Blowfish's `landing` layout (`homepage.layout: landing` in `params.yaml`). Its content comes from the front matter of `content/_index.md`, as described in [Homepage layout](https://blowfish.page/docs/homepage-layout/):

| Field | Shows as |
| --- | --- |
| `heroCaption` | The small line above the heading (defaults to the author's name) |
| `title` | The heading |
| `heroLead` | The intro paragraph (Markdown) |
| `heroButtons` | Buttons: `label`, `url`, and `style: outline` for an outline button |
| `heroImage` | The background image, a path inside `assets/` (for example `img/home-hero.jpg`) |
| `content_blocks` | Sections below the buttons (added by this starter; see below) |
| the page body | Text below the sections |

Recent posts are listed below it (`homepage.showRecent`).

**Sections (`content_blocks`)** are a small page builder this starter adds to the `landing` layout. Each item's `_name` names the template that renders it:

| `_name` | Template | Fields |
| --- | --- | --- |
| `blocks/stats` | `layouts/partials/blocks/stats.html` | `columns` (`"3"` or `"4"`), `items`: `value`, `label`, `text` |
| `blocks/feature-grid` | `layouts/partials/blocks/feature-grid.html` | `columns`, `items`: `icon` (a Blowfish icon name), `title`, `text`, `url`, `label` |

They copy the markup of Blowfish's `stats` and `feature-grid` shortcodes, so they look the same, but because they're front matter rather than shortcodes in the body, editors edit them in place in the Visual Editor. Keep `value` quoted (`"17"`): the editor's text fields only accept text. To add a new kind of section, see [Adding a section type](#adding-a-section-type).

Blowfish's other home layouts (`profile`, `hero`, `card`, `background`, `page`) still work: pick one in **Site settings → params.yaml → Home page → Home page layout**. They show the page body and the author profile. Only `landing` has in-place editing on the home page.

### Adding posts and pages

**In CloudCannon:**

- **Posts → Add → Post** creates `content/posts/<title>/index.md`, a folder named from the title. Posts need to be folders so their featured image and other images can sit beside them. The new post starts as a draft, dated now, and opens in the Content Editor (drafts aren't built, so there's no page for the Visual Editor yet). Turn off **Draft** to publish it.
- **Pages → Add → Page** creates `content/<title>.md` using the Page template (no date, reading time or author line). Add it to the menu in **Site settings → menus.en.yaml**. The home page can't be duplicated.
- Neither collection offers **Add folder**. Posts get their folder automatically, and pages live directly in `content/`.

The templates for new files are `.cloudcannon/schemas/post.md` and `page.md`. The folder naming is `create.path` in `cloudcannon.config.yml`.

**Locally:** create the post as a folder so the archetype and the bundle layout match:

```sh
hugo new content posts/my-new-post/index.md
```

Dates use the site's time zone (UTC, see [Time zone](#time-zone)). Hugo skips posts dated in the future until that time passes.

### Posts and images

- **One folder per post.** `content/posts/my-post/index.md` holds the text, and any images sit beside it. Blowfish resizes images in the post's folder for different screen sizes.
- **Featured image.** Name it `featured.jpg` (or `.png`/`.webp`). Blowfish uses it as the thumbnail, the image at the top of the post and the social sharing image. (Blowfish looks for `*feature*` for thumbnails and `*featured*` for sharing images, and `featured` matches both.) To use a different file, set `featureimage` in the post's front matter.
- **Site images** (logo, author photo, home page image) live in `assets/img/`, not `static/`, because Blowfish processes them. Refer to them without `assets/`, for example `img/author.jpg`.
- **Series.** Give posts the same `series` and a `series_order` to link them. See the two "Getting started" posts and [Series](https://blowfish.page/docs/series/).
- **Per-post options.** Most options under `[article]` in `params.yaml` can be overridden in a post's front matter (for example `showTableOfContents: false`). See the [front matter reference](https://blowfish.page/docs/front-matter/).

### Shortcodes

Every [Blowfish shortcode](https://blowfish.page/docs/shortcodes/) works in content. These ones are set up as CloudCannon snippets, so editors can insert and edit them without writing any syntax:

`alert`, `button`, `badge`, `lead`, `figure`, `youtubeLite`

The "Shortcode showcase" post uses them all. In the Visual Editor, shortcodes in a page's body show as cards you click to edit, rather than as they look on the page. That's why the home page's stats and features are sections instead. Other shortcodes still render, but the Content Editor shows them as an uneditable "unknown" block until you [add a snippet for them](#adding-a-snippet-for-another-shortcode).

### Customising the look

Try these in order, simplest first:

1. **Theme options** in Site settings, or `params.yaml`: colour scheme, light/dark mode, header style, layouts.
2. **CSS** in `assets/css/custom.css`, which Blowfish loads after its own stylesheet. Blowfish's Tailwind CSS is precompiled, so Tailwind classes the theme doesn't already use won't work in your templates. Write plain CSS here instead.
3. **Hooks.** Blowfish includes `layouts/partials/extend-head.html`, `extend-head-uncached.html` and `extend-footer.html` if you create them. This starter uses `extend-head.html` to load CloudCannon's editing script. Add to that file rather than replacing it.
4. **Overriding a template.** Copy a file from `_vendor/github.com/nunocoracao/blowfish/v3/layouts/` to the same path under `layouts/`, then edit the copy. Hugo uses yours instead of the theme's. Every override has to be kept in step with the theme when you update it, so override as little as possible.

## Editing in CloudCannon

This section is for developers. The editor-facing version is `.cloudcannon/README.md`.

| Where | What editors can do |
| --- | --- |
| **Pages → Home page**, Visual Editor | Click the caption, heading or intro to type. Add, remove and reorder buttons. Click the background image to replace it. Add, remove and reorder sections, and click the text in any stat or feature card to edit it. |
| **Posts / Pages**, Visual Editor | Click the title or body to type. Post options (tags, series, featured image, draft) are in the sidebar. |
| **Posts / Pages**, Content Editor | Write with a formatting toolbar. Insert shortcodes with the snippet button. Images dropped into a post are uploaded to the post's folder. |
| **Posts → Add → Post** | Creates a new folder with `index.md`, as a draft, opened in the Content Editor. |
| **Site settings → params.yaml** | Colour scheme, appearance, header, footer, home page and post display options. |
| **Site settings → languages.en.yaml** | Site title, description, copyright, logo, author name, photo, bio and profile links. |
| **Site settings → menus.en.yaml** | Header and footer menu links. |

Some things are deliberately edited in the sidebar rather than on the page:

- the featured image (it's found by file name)
- dates, tags and series
- the Posts list page's title and intro
- menus, footer and author profile

Making these editable in place would mean overriding more theme templates.

## How the CloudCannon integration works

- **`cloudcannon.config.yml`** defines:
  - three collections: Pages, Posts, and Site settings (the three editable config files)
  - the fields and labels editors see
  - image upload locations
  - the snippets for Blowfish's shortcodes
  - the structures for the home page's sections (`_structures.content_blocks`)
- **`.cloudcannon/schemas/`** are the starting points for new pages and posts. Each content file names its schema with `_schema`.
- **Editable regions.** CloudCannon's [editable-regions](https://github.com/CloudCannon/editable-regions) Hugo module (imported in `module.toml`) powers in-place editing. It works through `data-editable` / `data-prop` attributes in templates. `layouts/partials/extend-head.html` loads it; it runs only inside CloudCannon's Visual Editor.
- **Overridden theme files.** These are copies of Blowfish v3.8.0 templates, with editing attributes added. Each starts with a comment saying so.

  | File | Adds |
  | --- | --- |
  | `layouts/partials/home/landing.html` | Regions for the home caption, heading, intro, buttons, background image and body, and renders `content_blocks` |
  | `layouts/_default/single.html` | Regions for the title and body of posts and pages |
  | `layouts/partials/icon.html` | A fallback so icons still show in the Visual Editor (see below) |

  The starter's own templates are `layouts/partials/blocks/stats.html` and `feature-grid.html` (the home page sections) and `layouts/partials/extend-head.html`.

- **Icons in the editor.** The Visual Editor re-renders parts of the page (buttons, sections) with a copy of Hugo that runs in the browser. That copy can't see `assets/`, where Blowfish keeps its icons. The `icon.html` override reads the icon files directly when that happens, and `[params.editable_regions.additional_dirs]` in `hugo.toml` makes the theme's icon folder available to the editor.

- **`_vendor/`** holds the theme and the editable-regions module. CloudCannon's Visual Editor renders some templates (such as the hero buttons) in the browser, and it can only see templates inside the project. Vendoring also means the build needs no Go and no downloads apart from the editor's renderer, which Hugo fetches from GitHub during the build. **Commit `_vendor/`.**

## Updating Blowfish

1. Check the new release's supported Hugo range: `[module.hugoVersion]` in the theme's `config.toml`.
2. Update and re-vendor:

   ```sh
   hugo mod get -u github.com/nunocoracao/blowfish/v3
   hugo mod vendor
   ```

3. Re-apply the overrides. For each file in the table above, diff your copy against the new theme file and carry over the theme's changes, keeping the `data-editable` / `data-prop` attributes:

   ```sh
   diff _vendor/github.com/nunocoracao/blowfish/v3/layouts/partials/home/landing.html layouts/partials/home/landing.html
   diff _vendor/github.com/nunocoracao/blowfish/v3/layouts/_default/single.html layouts/_default/single.html
   diff _vendor/github.com/nunocoracao/blowfish/v3/layouts/partials/icon.html layouts/partials/icon.html
   ```

4. If the Hugo range moved, update `hugo_version` in `.cloudcannon/initial-site-settings.json` for new sites. For an existing CloudCannon site, change it in the site's build settings.
5. Build, and check the home page and a post in the Visual Editor.

To update editable-regions, run `hugo mod get github.com/CloudCannon/editable-regions@<new tag>` and then `hugo mod vendor`. Always use a release tag, because the module downloads a matching renderer from that GitHub release.

## Extending the starter

### Letting editors change a new option

Add the option to `params.yaml`, then give it an input under that file's `file_config` entry in `cloudcannon.config.yml`:

```yaml
file_config:
  - glob: config/_default/params.yaml
    _inputs:
      article.showZenMode:
        type: switch
        label: Show zen mode button
```

CloudCannon only shows options that are present in the file.

### Adding a section type

1. Create `layouts/partials/blocks/<name>.html`. Its context (`.`) is the section's own fields. Read site settings with `site.Params`, never `.Site`.
2. Add region attributes to what editors should edit in place, as in `blocks/stats.html`. The `data-prop` values are relative to the section.
3. Don't use `resources.Get` or image processing in it without a fallback: the Visual Editor re-renders sections in the browser, where `assets/` isn't available. Show a plain `<img src>` when `site.Params.ENV_CLIENT` is true.
4. Add a value under `_structures.content_blocks` in `cloudcannon.config.yml` with `_name: blocks/<name>` and every field the template reads, plus inputs and a preview.

### Adding a snippet for another shortcode

1. Read the shortcode's template in `_vendor/github.com/nunocoracao/blowfish/v3/layouts/shortcodes/<name>.html`. Each `.Get "x"` is an argument; `.Inner` means it wraps content.
2. Add an entry under `_snippets` in `cloudcannon.config.yml`. Copy `alert` for a shortcode that wraps content, or `youtubeLite` for one that doesn't.
3. Open a post that uses it in the Content Editor, edit it, save, and check that only the value you edited changed in the file.

### Making another template editable

Override the template (see [Customising the look](#customising-the-look)), then add attributes:

- **Text from front matter:** `data-editable="text" data-prop="<field>"` on the element that shows it. Add `data-type="text"` if the template renders it with `markdownify`.
- **The page body:** `data-editable="text" data-prop="@content"` on an element that contains only `{{ .Content }}`.
- **An image:** `data-editable="image" data-prop-src="<field>"` on the `<img>`.
- **A list:** `data-editable="array" data-prop="<field>"` on the container, with `data-editable="array-item"` (or an `<editable-array-item>` element) around each item.

See the [editable regions documentation](https://cloudcannon.com/documentation/developer-guides/set-up-visual-editing/) for more.

## Time zone

Dates are interpreted in UTC. To use your own time zone, set `timeZone` in `config/_default/hugo.toml` and `timezone` in `cloudcannon.config.yml` to the same value, for example `Europe/London`.

## Licence

Blowfish is MIT licensed by Nuno Coração. The placeholder images in this starter are generated gradients, free to reuse or replace.
