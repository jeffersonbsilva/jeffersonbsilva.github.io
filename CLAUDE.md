# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal portfolio and blog site for Jefferson Bruno Silva, built with Hugo (v0.146.0) and the PaperMod theme, deployed automatically to GitHub Pages at https://jeffersonbsilva.com.

## Development Commands

```bash
# Run local dev server with live reload
hugo server

# Build the site (mimics CI)
hugo --minify --baseURL "https://jeffersonbsilva.com/"

# Create a new post (use this archetype)
hugo new posts/<category>/<post-name>.md
```

No linter or test suite — the CI build itself is the validation step.

## Architecture

**Content → Hugo → GitHub Pages**

- `content/` — All Markdown pages and posts. Posts live under `content/posts/<category>/`.
- `layouts/` — Custom template overrides on top of PaperMod. Only two files exist here: a custom `index.html` homepage and a `partials/comments.html`. Everything else falls through to the theme.
- `themes/PaperMod/` — Git submodule. Do not edit files here; override them in `layouts/` instead.
- `static/` — Static assets (images, favicons) served at the root. Mirror the same category structure as `content/posts/` for post images.
- `hugo.yaml` — Single source of truth for all site config (theme, menus, search, social links, syntax highlighting).

## Content Conventions

**Frontmatter for posts:**
```yaml
---
title: "Post Title"
date: 'YYYY-MM-DD'
draft: false
description: "Brief description"
tags: [tag1, tag2]
categories: [category]
cover:
  image: "posts/<category>/image.jpg"
  alt: "Alt text"
  relative: true
ShowReadingTime: true
ShowShareButtons: true
showtoc: true
---
```

- `draft: true` excludes content from production builds (used on `projects.md` currently).
- Top-level pages (about, search, archives) use minimal frontmatter and specify `layout:` to select special rendering modes.
- Cover images go in `static/posts/<category>/` and are referenced with `relative: true`.

## Deployment

Push to `main` → GitHub Actions (`.github/workflows/hugo.yml`) builds with Hugo 0.146.0 + Dart Sass and deploys to GitHub Pages automatically. There is no staging environment.

The PaperMod submodule must be present for builds to succeed — CI checks it out with `submodules: recursive`.
