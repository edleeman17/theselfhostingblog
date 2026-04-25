# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Local dev server with live reload
hugo server -D

# Production build
hugo --minify

# New post
hugo new content/posts/my-post-title.md
```

Hugo version: **0.147.1 extended** (pinned in CI — match this locally).

Theme is a git submodule (`themes/hugo-blog-awesome`). After cloning:
```bash
git submodule update --init --recursive
```

## Architecture

This is a **Hugo static site** deployed to GitHub Pages via Actions (`.github/workflows/deploy.yml`). Push to `main` triggers build + deploy automatically.

**Root directory layout:**
- `theselfhostingblog/` — Hugo site (the actual codebase)
- `ghost-content.json` / `ghost-export.json` — source data from Ghost CMS migration
- `members-backup.csv` / `members-backup.json` — Ghost member export

**Content structure:**
- `content/posts/` — 49 blog posts, migrated from Ghost. Body is raw HTML (not Markdown prose) because Ghost exported HTML.
- `content/` top-level dirs (about-us, contact, donations, etc.) — standalone pages

**Post frontmatter pattern:**
```yaml
author: ed
title: 'Post Title'
date: 2021-02-14
description: 'Summary text'
cover:
  image: 'https://...'
  relative: false
tag:
  - 'Guides'   # or 'Ramblings'
```

Note: uses `tag` (singular) not `tags` — matches the taxonomy defined in `hugo.toml`.

**Theme customisation:**
- `assets/css/extended/custom.css` — overrides PaperMod; sets Mulish font, brand red `#d70606` for links/tags, fixes cover image sizing
- No custom layouts (the `layouts/` dir is empty except `layouts/rss/` which is unused)

**RSS/feed redirects:**
- `static/rss/index.html` and `static/feed/index.html` both redirect to `/index.xml` — preserves old Ghost feed URLs

**Config:** `hugo.toml` only (no `config/` directory splits). Key settings: dark-only theme (toggle disabled), syntax highlighting with `github-dark`, JSON search index output.
