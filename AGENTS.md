# AGENTS.md — wattanar.github.io

## What this is
Personal blog hosted on GitHub Pages. Uses [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) (v7.5.0).
Default branch: **master** (not main).

## Ruby version
- **Ruby 3.3** required (Chirpy requires `~> 3.1`, incompatible with Ruby 4.x)
- Use rbenv: `rbenv local 3.3.0`
- `bundle install` after Gemfile changes

## Developer commands
- `npm run serve` — start local dev server at `http://localhost:4000`
- `npm run build` — production build (`JEKYLL_ENV=production`)
- Lint → build order: `build` only (no lint scripts)

## Key conventions
- **Posts**: add markdown to `_posts/` with `YYYY-MM-DD-slug.md` naming
- **Drafts**: add markdown to `_drafts/` (no `published: false` front matter)
- **Excerpt separator**: `<!--more-->`
- **Config changes require restart**: `_config.yml` is NOT reloaded by `jekyll serve`
- **Theme is gem-based**: `_layouts/`, `_includes/`, `_sass/`, `_data/` are provided by the Chirpy gem — don't create them unless overriding
- **Tabs**: Chirpy uses `_tabs/` collection for top navigation pages (e.g., `_tabs/about.md`)

## Architecture notes
- `_config.yml` — main site config (theme_mode: dark, url: https://wattanar.github.io)
- `_posts/` — blog posts
- `_drafts/` — unpublished posts
- `assets/` — static files (favicon, images). CSS/JS provided by Chirpy gem
- CI: GitHub Actions (`.github/workflows/pages.yml`) deploys to GitHub Pages on master push

## Gotchas
- Chirpy uses `jekyll-archives` for tag/category pages — configured in `_config.yml`
- Permalink format: `/posts/:title/` (set in defaults)
- No test suite exists in this repo
- `docs/` directory was from old TeXt theme — removed
