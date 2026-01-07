# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a personal blog (https://flc.io) built with Hexo 8.0, a static site generator. The blog uses the Icarus theme and is primarily written in Chinese, covering categories like programming, reading notes, and personal reflections.

## Common Commands

### Development
```bash
# Start local development server (http://localhost:4000)
npm run server
# or
hexo server

# Build static site (outputs to public/)
npm run build
# or
hexo generate

# Clean cache and public folder
npm run clean
# or
hexo clean
```

### Creating Content
```bash
# Create new post (creates file in source/_posts/)
hexo new post "Post Title"

# Create new draft
hexo new draft "Draft Title"

# Publish a draft to posts
hexo publish draft "Draft Title"
```

## Architecture & Structure

### Content Organization

- **Posts**: Located in `source/_posts/YYYY/filename.md`
  - Organized by year in subdirectories
  - Written in Markdown with YAML front matter
  - Use `<!-- more -->` to define excerpt shown on homepage

- **Drafts**: Located in `source/_drafts/`
  - Not published until moved to `_posts/`

- **Static Pages**: Special pages like `about/` and `open-source/`

- **Labs & Shared**: Special directories under `source/`
  - `source/labs/`: Experimental projects (e.g., space-bridge)
  - `source/shared/`: Shared resources (e.g., 2025-26-cn-living-insight)
  - These are excluded from Hexo rendering via `skip_render` in `_config.yml`

### Configuration Files

- **`_config.yml`**: Main Hexo configuration
  - Site metadata, permalinks, deployment settings
  - `skip_render` for labs/** and shared/** directories
  - Plugin configurations (RSS feed, sitemap, aplayer)

- **`_config.icarus.yml`**: Icarus theme configuration
  - Navigation menu, widgets, appearance
  - PWA manifest settings
  - Social links and analytics

- **`vercel.json`**: Vercel deployment configuration
  - Domain redirects (www.flc.io and blog.flc.io → flc.io)
  - Labs redirect (filament-manager → fil.flc.io)

### Post Front Matter Format

Standard format for blog posts:
```yaml
---
title: "Post Title"
date: YYYY-MM-DD HH:mm:ss
categories:
- Main Category
- Sub Category  # Optional nested category
tags:
- Tag1
- Tag2
cover: https://example.com/image.jpg  # Optional
toc: true  # Optional table of contents
---
```

### Categories vs Tags

- **Categories**: Hierarchical structure (e.g., 编程 > 后端)
  - Mapped in `_config.yml` to English slugs (分享→share, 生活→life)
  - Common: 摘录, 编程, 阅读, 随记

- **Tags**: Flat labels for topics
  - Common: Go, PHP, Redis, LeetCode, Git

## Deployment

The site is deployed via Vercel:
- Domain redirects are configured in `vercel.json`
- Main domain: https://flc.io
- Redirects from www.flc.io and blog.flc.io to flc.io
- Filament manager redirects to separate domain (fil.flc.io)

## Key Plugins & Features

- **hexo-generator-feed**: Generates RSS/Atom feed at `/atom.xml`
- **hexo-generator-sitemap**: Generates sitemap at `/sitemap.xml`
- **hexo-tag-aplayer**: Music player support in posts
- **hexo-admonition**: Callout/admonition boxes support

## Important Notes

- The blog is primarily in Chinese (zh-CN)
- Timezone is Asia/Shanghai
- Post permalinks use `:name/` format (no date in URL)
- The `public/` directory is generated and should not be edited directly
- `db.json` is Hexo's database cache (regenerated on build)
