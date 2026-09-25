# Welcome

This site is a blog. You can change everything on it here: the pages, the posts, and how the site looks.

## Quick links

- [Pages](cloudcannon:collections/pages): the home page and standalone pages such as About
- [Posts](cloudcannon:collections/posts): blog posts
- [Site settings](cloudcannon:collections/settings): colours, header style, author profile and menus
- [Tags and categories](cloudcannon:collections/post_options): the tags, categories and series you can pick for a post

## The home page

Open **Pages → Home page**. In the Visual Editor you can:

- click the small caption, the big heading or the intro and type
- add, remove, reorder and edit the buttons
- click the background image to replace it
- edit the stats and feature cards further down the page by clicking their text
- add, remove and reorder sections (stats rows and feature grids) with the controls that appear when you hover over one, or in the **Sections** list in the sidebar

## Posts

- **Write a post:** open **Posts** and choose **Add → Post**. A new post starts as a draft. Give it a title and some text, and turn off **Draft** when it's ready. Drafts don't appear on the live site, so they open in the Content Editor rather than the Visual Editor.
- **Edit a post:** open it and click its title or text to type. Use the sidebar for tags, categories, series, the date and the featured image.
- **Images:** images you add to a post are stored with that post. Its featured image (used in lists, at the top of the post and when it's shared) is the image called `featured.jpg`. Replace that file, or choose another image in **Featured image**.
- **Series:** give posts the same **Series** name, and a **Position in series**, to link them together.

## Components in the text editor

Use the snippet button in the toolbar to insert:

- **Alert:** a highlighted note
- **Button:** a link styled as a button
- **Badge:** a small label in a sentence
- **Lead paragraph:** larger introductory text
- **Figure:** an image with a caption
- **YouTube video**

Click an inserted component to edit it.

## Site settings

- **params.yaml** controls the theme:
  - colour scheme and light/dark mode
  - header style
  - the home page layout and recent posts
  - what posts show (date, reading time, table of contents, sharing links, and so on)
  - the footer
- **languages.en.yaml** holds the site title, description and copyright notice, the logo, and the author profile (name, photo, headline, bio and profile links).
- **menus.en.yaml** holds the header and footer menus. Each link points to a page (for example `posts` or `about`) or to a web address. Lower **Order** numbers come first.

Changes to settings appear on the site after it rebuilds.

## Tags, categories and series

A post's **Tags**, **Categories** and **Series** fields offer the lists in **Tags and categories**. Add a new one there first, and every post can pick it. You can also type a new one straight into a post, but it's added to that post only and won't show up in the list for other posts.

## New pages

Choose **Pages → Add → Page**. A new page opens with a preview of the About page until the site rebuilds. To add it to the menu, edit **Site settings → menus.en.yaml**.
