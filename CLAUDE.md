---
description: Project guidance for Wei Guo's Jekyll personal website
alwaysApply: true
---

# CLAUDE.md

This is the single source of agent guidance for this repository. `AGENTS.md`
and `.cursor/rules/CLAUDE.mdc` are intended to be symlinks to this file so
Claude Code, Codex, and Cursor read the same project instructions.

## What This Is

Academic personal website for Wei Guo, a Georgia Tech ML PhD student.
The site is built with Jekyll using the al-folio theme and deployed to GitHub
Pages from the generated `_site` directory.

## Build & Serve Commands

```bash
# Local development with the prebuilt al-folio Docker image
docker-compose up                    # serves at http://localhost:8080

# Local development with the repo Dockerfile
docker-compose -f docker-local.yml up # serves at http://localhost:8080

# Native Ruby
bundle install
bundle exec jekyll serve --lsi       # serves at http://localhost:4000

# Production build, matching CI
JEKYLL_ENV=production bundle exec jekyll build

# Legacy build helper
bin/cibuild                          # runs bundle exec jekyll build --lsi
```

CI is defined in `.github/workflows/deploy.yml`. It runs on pushes and pull
requests to `main`/`master`, uses Ruby 3.2.2 with Bundler cache, installs
`jupyter` and `mermaid.cli`, builds with `JEKYLL_ENV=production`, and deploys
`_site` via `JamesIves/github-pages-deploy-action@v4` except on pull requests.
Treat `bin/deploy` as a legacy/manual deploy script because it performs branch
switching and force-push style publication.

### Local Preview Procedure

When the user asks to compile/build and view the website locally, use the
native Jekyll setup that has already been verified on this machine:

```bash
export PATH="/Users/weiguo/Library/Python/3.9/bin:/opt/homebrew/opt/ruby@3.2/bin:$PATH"
JEKYLL_ENV=production bundle exec jekyll build
bundle exec jekyll serve --lsi --host 127.0.0.1 --port 4000
open "http://127.0.0.1:4000/"
```

Operational notes:

- Ruby 3.2 is installed via Homebrew at `/opt/homebrew/opt/ruby@3.2/bin`.
- Jupyter is installed in the user Python bin directory and must be on `PATH`
  for `jekyll-jupyter-notebook`.
- ImageMagick is installed via Homebrew and provides `convert` for responsive
  WebP generation.
- Run the production build first when validating changes; Sass deprecation
  warnings from al-folio are expected and do not block preview.
- Start `jekyll serve` as a long-running/background command, then open
  `http://127.0.0.1:4000/`. Stop the server with Ctrl-C when done.
- Put screenshots and other temporary inspection files under `tmp/`, which is
  gitignored and periodically cleaned by the user.

## Architecture

### Content Authoring

- Home page: `_pages/about.md`, rendered with `_layouts/about.html`.
- Publications page: `_pages/publications.md`.
- Talks page: `_pages/talks.md`, rendered from `_data/talks.yml` via
  `_includes/talks.html`. Navbar order is About, Blog, then `nav_order`
  (Publications `1`, Talks `2`).
- Publications data: `_bibliography/papers.bib`, rendered by `jekyll-scholar`
  through `_layouts/bib.html`. `selected = {true}` marks featured papers.
- News/announcements: `_news/*.md`, configured as the `news` collection.
- Blog: `blog/index.html` plus archive layouts are configured; `_posts/` is
  currently absent/empty.
- CV/resume: `_data/cv.yml`, `assets/json/resume.json`, and `assets/cv/`.
- Coauthor links: `_data/coauthors.yml`, used to auto-link names in publication
  lists.

### Theme And Layout System

- `_layouts/` contains 11 layouts, including `default.html`, `about.html`,
  `bib.html`, `post.html`, `page.html`, `cv.html`, archive layouts, and
  `distill.html`.
- `_includes/` contains theme partials, including script loaders, resume/CV
  sections, repository cards, news, selected papers, talks, and media embeds.
- `_sass/` contains 6 SCSS partials. `assets/css/main.scss` imports variables,
  themes, layout, base styles, Distill styles, and CV styles.
- `_plugins/` contains 4 custom Ruby plugins: details tag support, external RSS
  posts, a Liquid file-exists tag, and BibTeX custom-field hiding.

### Configuration

Most site-wide behavior lives in `_config.yml`:

- Identity and social metadata for Wei Guo.
- Fixed navbar/footer with `max_width: 800px`.
- `collections.news` and `collections.projects`.
- Jekyll plugins including archives, diagrams, scholar, sitemap, imagemagick,
  Jupyter notebook rendering, minifier, email protection, and pagination.
- `scholar:` highlights `Wei Guo`, uses APA style, reads
  `_bibliography/papers.bib`, and groups entries by year descending.
- `filtered_bibtex_keywords` hides internal publication fields such as `abbr`,
  `arxiv`, `code`, `poster`, `slides`, `preview`, and `selected`.
- Optional features include MathJax 3.2.0, dark mode, masonry, medium zoom, and
  a scroll progress bar.
- `imagemagick:` generates responsive WebP images from `assets/img/` at 480,
  800, and 1400 px widths.

### Static Assets

`assets/` holds styles, JavaScript, images, PDFs, JSON data, notebooks, CV
sources, and Plotly assets. Publication PDFs, posters, and slides live under
`assets/pdf/`; publication preview images should be placed under
`assets/img/publication_preview/` when used by BibTeX entries.

## Common Edits

### Adding A New Publication

1. Add a BibTeX entry to `_bibliography/papers.bib`.
2. Use `abbr` for the venue badge and `selected = {true}` for homepage feature.
3. Prefer existing optional fields when available: `pdf`, `html`, `code`,
   `poster`, `slides`, `arxiv`, `preview`, and `website`.
4. Put preview thumbnails in `assets/img/publication_preview/`.
5. Add missing coauthor homepage mappings to `_data/coauthors.yml`.

### Adding A Talk

1. Add an entry to `_data/talks.yml` (newest first is automatic by `date`).
2. Prefer existing optional fields: `venue`, `location`, `description`,
   `slides`, `video`, `poster`, and `papers`.
3. Put HTML slides under `assets/html/` and PDFs under `assets/pdf/`.

### Adding A News Item

Create a Markdown file in `_news/`, for example `_news/announcement_3.md`:

```yaml
---
layout: post
date: YYYY-MM-DD
inline: true
---
Your announcement text here.
```

### Editing The Home Page

Edit `_pages/about.md` for profile text, announcements placement, and selected
publication display. Keep major layout changes in `_layouts/about.html` or the
relevant `_includes/` partial instead of embedding complex HTML in Markdown.

## Agent Notes

- Keep this file concise and update it when build commands, content locations,
  or agent entry points change.
- Do not edit generated output in `_site`; change source files instead.
- Preserve the al-folio structure unless a site-specific customization clearly
  belongs in `_layouts/`, `_includes/`, `_sass/`, or `_plugins/`.
- When adding publication metadata, prefer fields already supported by the
  theme and `_plugins/hideCustomBibtex.rb` over inventing new ones.
- Before changing deployment behavior, inspect `.github/workflows/deploy.yml`
  and avoid relying on the legacy `bin/deploy` script unless explicitly asked.
