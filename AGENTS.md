# Repository Guidelines

## Project Structure & Module Organization

This repository is a Jekyll-powered GitHub Pages site. Core site settings live in `_config.yml`, navigation data is in `_data/navigation.yml`, layouts are in `_layouts/`, and reusable HTML fragments are in `_includes/`. Main content pages are stored under `_pages/`; the homepage is `_pages/about.md`, and the projects index is `_pages/projects.md`. Styles are split between `assets/css/main.scss` and partials in `_sass/`. Static assets belong in `images/`, `assets/`, or `files/` for downloadable documents such as `files/CV.pdf`.

Generated output is written to `_site/` by Jekyll and should not be edited directly.

## Build, Test, and Development Commands

Use the project’s Ruby dependencies through Bundler:

```bash
bundle install
bundle exec jekyll build
bundle exec jekyll liveserve
```

`bundle install` installs the GitHub Pages/Jekyll gem set from `Gemfile.lock`. `bundle exec jekyll build` renders the site into `_site/` and is the main validation step. `bundle exec jekyll liveserve` runs the local development server with live reload; `run_server.sh` wraps this command.

When running commands for this repository as an agent, use the `codex` conda environment first:

```bash
source /home/CUIPeng/miniconda3/etc/profile.d/conda.sh
conda activate codex
```

## Coding Style & Naming Conventions

Use Markdown with YAML front matter for pages. Prefer concise section headings and keep existing English site copy style unless intentionally localizing content. Use two-space indentation in YAML files. Keep URLs lowercase where possible, but match actual filename case exactly for static files, for example `/files/CV.pdf`.

For Sass and HTML, follow existing Minimal Mistakes conventions and avoid broad restyling unless the change requires it.

## Testing Guidelines

There is no dedicated automated test suite. Validate changes by running:

```bash
bundle exec jekyll build
```

For content or navigation changes, inspect the generated page locally with `jekyll liveserve` when visual layout matters. Check that internal links, anchors, and static files resolve correctly.

## Commit & Pull Request Guidelines

Recent history uses short, informal commit messages. Prefer clearer imperative messages going forward, such as `Add projects page`, `Update education entry`, or `Fix CV navigation link`.

Pull requests should include a short summary, the pages or files changed, verification performed, and screenshots for visible UI changes. Link related issues when applicable and avoid committing generated `_site/` output unless the deployment workflow explicitly requires it.
