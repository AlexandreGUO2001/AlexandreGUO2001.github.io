# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

Academic personal website for Wei Guo (郭纬), a Georgia Tech ML PhD student. Built with **Jekyll** using the **al-folio** theme (by alshedivat), deployed to GitHub Pages.

## Build & Serve Commands

```bash
# Local development (recommended on Windows — avoids Ruby setup)
docker-compose up                    # serves at http://localhost:8080

# Native Ruby
bundle install
bundle exec jekyll serve --lsi       # serves at http://localhost:4000

# Production build (used in CI)
JEKYLL_ENV=production bundle exec jekyll build
```

CI (`deploy.yml`) also installs `pip3 install --upgrade jupyter` and `npm install -g mermaid.cli` before building. Deployment goes to the `gh-pages` branch via `JamesIves/github-pages-deploy-action@v4`.

## Architecture

### Content authoring

- **Home page**: `_pages/about.md` — profile, announcements, selected publications
- **Publications**: `_bibliography/papers.bib` (BibTeX, rendered by `jekyll-scholar`). The `selected: true` field marks papers shown on the home page. Custom fields (`abbr`, `arxiv`, `poster`, `slides`, `code`) are filtered from raw bib output by `_plugins/hideCustomBibtex.rb`
- **News/announcements**: individual Markdown files in `_news/`
- **Blog posts**: `_posts/` (currently empty), with archives enabled for year/tag/category
- **CV data**: `_data/cv.yml` and `assets/json/resume.json` (JSON Resume schema)
- **Coauthor links**: `_data/coauthors.yml` — maps coauthor names to homepage URLs for auto-linking in publication lists

### Theme & layout system

- `_layouts/` — 11 layouts; `default.html` is the base, `about.html` is the home page, `bib.html` renders individual bibliography entries
- `_includes/` — 44 partials; JS dependencies are loaded via `_includes/scripts/`
- `_sass/` — 6 SCSS files; `_themes.scss` handles light/dark mode, `_base.scss` is the main stylesheet
- `_plugins/` — 4 custom Ruby plugins (details tag, external posts, file-exists check, bib keyword filter)

### Key configuration

All site-wide settings live in `_config.yml`:
- `scholar:` block configures bibliography (author highlighting with `last_name: [Guo]`, APA style, grouped by year descending)
- `enable_math: true` — MathJax 3.2.0
- `enable_darkmode: true` — light/dark toggle
- `max_width: 800px`, fixed navbar and footer
- `imagemagick:` — auto-generates responsive WebP images from `assets/img/`

### Static assets

`assets/` contains CSS, JS, images, PDFs (papers/posters), and Jupyter notebooks. Images are auto-converted to responsive WebP at 480/800/1400px widths.

## Adding a New Publication

1. Add a BibTeX entry to `_bibliography/papers.bib`
2. Use `abbr` for the venue badge, `arxiv` for arXiv link, `selected: true` to feature on homepage
3. Optional fields: `pdf`, `html`, `code`, `poster`, `slides`, `preview` (thumbnail image filename in `assets/img/publication_preview/`)
4. Coauthor auto-linking requires a matching entry in `_data/coauthors.yml`

## Adding a News Item

Create a Markdown file in `_news/` (e.g., `announcement_8.md`) with front matter:
```yaml
---
layout: post
date: YYYY-MM-DD
inline: true
---
Your announcement text here.
```
