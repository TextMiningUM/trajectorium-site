# Copilot Instructions — Trajectorium Site

## Project overview
Quarto-based bilingual website (English + Dutch) deployed to GitHub Pages at `balyzai.nl`.
- `en/` — English Quarto project → outputs to `_site/en/`
- `nl/` — Dutch Quarto project → outputs to `_site/nl/`
- `images/` — shared assets used by both language sites
- `custom.scss` — shared theme (root level), referenced as `../custom.scss` in both `_quarto.yml`
- `.github/workflows/publish.yml` — builds both sites and assembles `_publish/`

## Rule: always mirror EN and NL
Every change — content, styling, layout, navigation — must be applied to **both `en/` and `nl/` simultaneously** in a single `multi_replace_string_in_file` call. Never commit a change to one language only.

## Rule: shared images
- All images — including those used in blog posts, notebooks, and slides — must be stored in the root `images/` folder.
- Never place images inside `en/` or `nl/` subfolders.
- Reference them in `.qmd` files as `../images/filename.png`.
- Quarto resolves this to an absolute `/images/` path at runtime.
- The deploy workflow already contains `cp -r images _publish/images` — **do not remove this line**.
- When adding new image subfolders, add a matching `cp -r` line in the workflow.

## Rule: new sections / subfolders
When creating a new content section (e.g. a new topic folder inside `en/` or `nl/`):
1. Create the folder + `index.qmd` in **both** `en/<section>/` and `nl/<section>/`.
2. Add the nav entry to **both** `en/_quarto.yml` and `nl/_quarto.yml`.
3. Verify the workflow copies any new static assets.

## Rule: deploy workflow integrity
The assemble step in `.github/workflows/publish.yml` must always produce:
```
_publish/
  index.html          ← root redirect
  en/                 ← compiled EN site
  nl/                 ← compiled NL site
  images/             ← shared images (cp -r images _publish/images)
```
If new shared asset folders are added to the repo root, add them here too.

## Rule: styling
- Theme file: `custom.scss` at repo root.
- Both `_quarto.yml` files reference it as `../custom.scss` under `format.html.theme`.
- Always update `custom.scss` for visual changes; never add inline `<style>` blocks.

## Rule: no emoji
Never use emoji or emoticons in any `.qmd` content file, page titles, navigation labels, card headings, or button text. Plain text and Unicode symbols (→, —) are allowed.

## Rule: consistent styling
All new pages must follow the same layout patterns as the homepage: use CSS classes from `custom.scss` (`.feature-card`, `.hero-banner`, `.diagram-section`, etc.). Never add page-specific inline `<style>` blocks or override styles outside `custom.scss`.

## Rule: project documentation language
All project documentation (README, copilot-instructions, workflow comments, `.qmd` front matter comments) must be written in English.

## Rule: page layout
- Homepage (`index.qmd`): `page-layout: full`, `toc: false`
- Content pages (blog, notebooks, slides): default layout with TOC

## Rule: git workflow
- Branch: `main` — push directly, no PRs needed for content changes.
- Commit messages: concise, imperative, e.g. `Add: NL blog post on RAG`.
- After every change: `git add`, `git commit`, `git push`.

## Domain & config
- Live URL: `https://balyzai.nl`
- EN site URL: `https://balyzai.nl/en`
- NL site URL: `https://balyzai.nl/nl`
- CNAME: `balyzai.nl`
