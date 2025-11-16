# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Jekyll-based personal blog and portfolio website for Ari Biswas, hosted on GitHub Pages. The site uses the Beautiful Jekyll theme (remote theme `daattali/beautiful-jekyll@6.0.1`) and focuses on content related to artificial intelligence, robotics, reinforcement learning, and software engineering.

## Development Commands

### Local Development
```bash
# Install dependencies
bundle install

# Serve the site locally with live reload
bundle exec jekyll serve

# Serve with drafts visible
bundle exec jekyll serve --drafts

# Build the site to _site/ directory
bundle exec jekyll build
```

### Python Environment (for blog post examples)
Some blog posts demonstrate Python code integration with MATLAB. To work with these examples:

```bash
# Create and activate virtual environment
python -m venv env
source ./env/bin/activate

# Install dependencies (for posts like the Unitree robot training)
pip install gymnasium[mujoco]

# Run MATLAB from terminal with activated environment
/Applications/MATLAB_R2024b.app/bin/matlab
```

## Architecture

### Site Structure

- `_config.yml` - Main Jekyll configuration with theme settings, site metadata, SEO, analytics, and Beautiful Jekyll customizations
- `_posts/` - Blog posts in markdown format following Jekyll's `YYYY-MM-DD-title.markdown` naming convention
- `assets/` - Static assets organized by post date (e.g., `assets/2025-01-09-rl-train-unitree-robot/`)
- `_site/` - Generated site output (excluded from git, created by Jekyll build)
- `about.md` - About page
- `index.markdown` - Homepage using the `home` layout
- `Gemfile` - Ruby dependencies, using `github-pages` gem version ~> 232

### Content Organization

Blog posts use YAML front matter with the following key fields:
- `layout: post` - Uses the Beautiful Jekyll post layout
- `title`, `subtitle`, `excerpt` - Content descriptions
- `seo_title`, `seo_description` - SEO metadata
- `tags` - Topic tags (e.g., robotics, reinforcement learning, mujoco, MATLAB)
- `thumbnail-img` - Post preview image
- `gh-repo`, `gh-badge` - GitHub repository integration for showcasing projects
- `date` - Post date in format `YYYY-MM-DD HH:MM:SS -OFFSET`

### Theme Configuration

The site uses Beautiful Jekyll with customizations defined in `_config.yml`:
- Custom color scheme (navbar, footer, links)
- Google Analytics integration (gtag: G-KDT2BM43QG)
- Social media links (GitHub: aribiswas, LinkedIn: biswasic)
- SEO optimization with jekyll-seo-tag plugin
- Post search functionality enabled
- Pagination set to 5 posts per page

### Jekyll Plugins

Required plugins (via github-pages gem):
- `jekyll-paginate` - Post pagination
- `jekyll-sitemap` - Sitemap generation
- `jekyll-gist` - GitHub Gist embedding
- `jekyll-feed` - RSS feed generation
- `jekyll-include-cache` - Performance optimization
- `jekyll-archives` - Archive page generation
- `jekyll-seo-tag` - SEO metadata

## Creating New Blog Posts

1. Create a new file in `_posts/` following the naming pattern: `YYYY-MM-DD-title.markdown`

2. Add front matter with required fields:
```yaml
---
layout: post
title: "Your Post Title"
subtitle: Optional subtitle
excerpt: Brief description for previews
seo_title: "SEO-optimized title"
seo_description: SEO description
tags: [tag1, tag2, tag3]
thumbnail-img: assets/YYYY-MM-DD-title/image.png
date: YYYY-MM-DD HH:MM:SS -0500
---
```

3. Create corresponding assets directory: `assets/YYYY-MM-DD-title/` for images, videos, and other media

4. Use markdown for content. Beautiful Jekyll supports:
   - Box notes: `{: .box-note}` on line before text
   - Code blocks with syntax highlighting
   - Embedded videos: `<video>` tags with local paths
   - Math rendering with KaTeX (available in `_site/assets/katex/`)

## Important Notes

- The site is configured for timezone `America/Toronto`
- Permalink structure: `/:year-:month-:day-:title/`
- The `_site/` directory is generated and should not be edited directly
- When referencing assets in posts, use absolute paths from site root: `/assets/...`
- SEO keywords defined in config: "rl,robotics,ai,artificial intelligence,software engineering,reinforcement learning,python,matlab"
- Comments are enabled by default for all posts (can be configured per-post or via comment platforms in config)

## Deployment

This site is designed for GitHub Pages deployment. Changes pushed to the `main` branch are automatically built and deployed by GitHub Pages using the github-pages gem.
