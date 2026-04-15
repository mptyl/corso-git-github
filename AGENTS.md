# AGENTS.md

## Project

Italian-language Git/GitHub training course built with MkDocs and deployed to GitHub Pages. All content is Markdown under `docs/`.

## Build & Preview

```bash
pip install -r requirements.txt   # mkdocs + mkdocs-material
mkdocs serve                      # local dev server at localhost:8000
mkdocs build --strict             # production build (CI uses --strict, warnings = errors)
```

## Deploy

Pushes to `main` auto-deploy via `.github/workflows/pages.yml` using GitHub Pages. No manual deploy step.

## Structure

- `docs/` — course lessons (`lezione-00.md` through `lezione-09.md`) and `index.md`
- `mkdocs.yml` — nav, theme config, markdown extensions
- `site/` — build output (gitignored)
- `README.md` — course overview and syllabus (not part of the MkDocs site)

## Conventions

- Language: Italian (including UI labels in `mkdocs.yml`)
- Theme: mkdocs-material with light/dark toggle
- CI builds with `--strict` — any broken link or warning fails the build
