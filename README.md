# username.github.io — personal site

Built with [Quarto](https://quarto.org), deployed to GitHub Pages.

## Structure

```
.
├── _quarto.yml              # site config: nav, footer, theme, output-dir
├── index.qmd                # home / about page (about: trestles template)
├── research/
│   ├── index.qmd            # publications & working papers
│   └── references.bib       # BibTeX for your own papers
├── projects/
│   ├── index.qmd            # auto-generated grid (Quarto "listing")
│   ├── example-shiny-dashboard.qmd
│   └── example-r-package.qmd
├── blog/
│   ├── index.qmd            # auto-generated post list + RSS feed
│   └── posts/
│       ├── _metadata.yml    # freeze: true for the whole posts/ folder
│       └── 2026-01-01-welcome-post/
│           └── index.qmd
├── styles/
│   ├── styles.scss          # light theme: scss:defaults + scss:rules
│   └── styles-dark.scss     # dark theme overrides
├── files/
│   ├── img/                 # profile photo, favicon, logo, project thumbs
│   ├── papers/               # PDFs linked from research/index.qmd
│   └── cv/cv.pdf
├── 404.qmd
├── robots.txt
├── .nojekyll                 # tells GitHub Pages to skip Jekyll processing
├── .gitignore                 # ignores /_site, /.quarto — NOT /_freeze
├── LICENSE.md
└── .github/
    ├── workflows/publish.yml  # render + deploy on every push to main
    └── dependabot.yml         # keeps the Action versions current
```

This mirrors the pattern used by most Quarto-based academic/personal
sites (e.g. Andrew Heiss, Mine Çetinkaya-Rundel, Grant McDermott,
Mickaël Canouil, Emil Hvitfeldt, Fernando Rios-Avila, Bastián Olea
Herrera): an `about: trestles` landing page, a listing-driven blog and
project index, a dedicated research/publications page, and a small
SCSS file layered on top of a Bootswatch theme instead of a from-scratch
stylesheet.

## One-time setup

1. **Rename placeholders.** Search the repo for `username`,
   `Jane Doe`, and `jane.doe@example.com` and replace with your own.
2. **Add real assets** in `files/img/` (profile photo, favicon,
   optional logo) and `files/cv/cv.pdf`. Each placeholder folder has a
   `*.README.txt` note telling you what belongs there — delete those
   notes once you've added the real files.
3. **Pick your accent colour.** Edit `$primary` / `$secondary` in
   `styles/styles.scss` (and the dark-mode equivalents in
   `styles-dark.scss`). Everything else (links, navbar, buttons)
   derives from these two variables — avoid hard-coding new colours
   elsewhere in the SCSS.
4. **Install the icon extensions** used in `_quarto.yml`/`index.qmd`
   (Font Awesome for `{{< fa ... >}}`, Academicons for
   `{{< ai google-scholar >}}` / `{{< ai orcid >}}`):

   ```bash
   quarto add quarto-ext/fontawesome
   quarto add schochastics/academicons
   ```

   This creates an `_extensions/` folder — commit it to version control.
5. **Create the repo** on GitHub, named either `username.github.io`
   (a *user site*, served at the domain root) or any other name (a
   *project site*, served at `username.github.io/reponame/`). If you
   go with a project site, update `site-url` and `repo-url` in
   `_quarto.yml` accordingly.

## Local preview

```bash
quarto preview
```

## Publish to GitHub Pages

Two supported approaches — pick one:

### A. `quarto publish` (simplest, manual)

```bash
quarto publish gh-pages
```

Run this once locally. It creates a `gh-pages` branch, renders the
site, and pushes it. Set GitHub → **Settings → Pages → Source** to the
`gh-pages` branch (for a *user site* you must switch this manually the
first time; for a *project* repo Quarto/GitHub usually detect it
automatically).

### B. GitHub Actions (automated, recommended once the site is live)

`.github/workflows/publish.yml` is already set up to render and
deploy on every push to `main`, using Quarto's official
`quarto-dev/quarto-actions/publish` action. Requirements:

- Run `quarto publish gh-pages` locally **once** first (step A above)
  — this generates the `_publish.yml` file the Action needs.
- Under **Settings → Actions → General → Workflow permissions**,
  enable "Read and write permissions".
- If any pages execute R/Python code, either rely on `freeze: auto`
  (already set in `_quarto.yml`, computations run locally and get
  cached in `/_freeze`, which **must** be committed) or uncomment the
  R/Python setup steps in the workflow to execute code in CI instead.

## Notes on the pieces

- **`freeze: auto`** in `_quarto.yml` means Quarto only re-executes
  code cells whose inputs changed, and stores results in `/_freeze/`.
  Commit that folder — it's what lets CI publish without needing R/Python
  installed, and keeps old blog posts reproducible even if a package
  API changes later.
- **Listings** (`projects/index.qmd`, `blog/index.qmd`) are Quarto's
  built-in way to auto-generate a filterable/sortable grid or feed from
  a folder of `.qmd` files — add a new project or post by creating a
  new file with the right YAML front matter (`title`, `date`, `image`,
  `categories`, `description`); the index page updates itself.
  Docs: <https://quarto.org/docs/websites/website-listings.html>
- **`about: trestles`** is one of five built-in About-page templates
  (`jolla`, `trestles`, `solana`, `marquee`, `broadside`) — good for a
  photo + bio + links layout typical of academic homepages.
  Docs: <https://quarto.org/docs/websites/website-about.html>
- **SCSS structure**: each theme file has two sections —
  `/*-- scss:defaults --*/` for Sass variables that override Bootstrap
  (colours, fonts, spacing) and `/*-- scss:rules --*/` for actual CSS
  rules/selectors. Keeping variables and rules in these two blocks
  (rather than one flat file) is the pattern Quarto's HTML theming
  expects and what most of the reference sites above follow.
  Docs: <https://quarto.org/docs/output-formats/html-themes.html>
- **RSS feed**: `feed: true` on the blog listing generates
  `blog/index.xml` automatically, already linked from the footer.
- **Sitemap & robots.txt**: `sitemap: true`-equivalent is on by
  default for Quarto websites (`sitemap.xml` is generated at render
  time); `robots.txt` in the project root just needs to point at it.

## Adding a new blog post

```bash
mkdir -p blog/posts/2026-02-01-my-new-post
```

```yaml
---
title: "My New Post"
description: "One sentence summary."
date: 2026-02-01
categories: [econometrics, R]
image: thumb.png
---
```

## Adding a new project

Create `projects/my-project.qmd` with `title`, `description`, `date`,
`image`, and `categories` in the YAML front matter — it will
automatically appear in the `projects/index.qmd` grid.
