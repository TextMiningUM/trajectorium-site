# Trajectorium — Bilingual Quarto Site

## Why two sub-projects instead of one?

Quarto has no built-in i18n support for websites. The standard pattern is therefore
**two separate Quarto projects** (`/en` and `/nl`), each with its own `_quarto.yml`,
navbar, and `lang:` setting. A root `index.html` redirects visitors to `/en/` by default.

```
trajectorium-site/
├── index.html              ← language redirect to /en
├── images/                 ← shared images for both language sites
├── custom.scss             ← shared theme, referenced as ../custom.scss
├── en/
│   ├── _quarto.yml         ← navbar, site-url, hreflang for EN
│   ├── index.qmd
│   ├── blog/
│   │   └── what-is-an-ai-agent.qmd
│   ├── notebooks/
│   └── slides/
└── nl/
    ├── _quarto.yml
    ├── index.qmd
    ├── blog/
    │   └── wat-is-een-ai-agent.qmd
    ├── notebooks/
    └── slides/
```

## Building — automatically via GitHub Actions (recommended)

A workflow is already configured at `.github/workflows/publish.yml`. It:

1. Renders `en/` and `nl/` separately with Quarto
2. Assembles the output together with `index.html` and `images/` into a single `_publish/` folder
3. Publishes that folder to GitHub Pages

**Steps to activate:**

1. In the repo: **Settings → Pages → Source** → select **"GitHub Actions"**
2. The `CNAME` file is already set to `balyzai.nl`
3. Commit and push to the `main` branch — the workflow starts automatically
4. Monitor progress under the **Actions** tab in the repo
5. Once the first run succeeds, enable **"Enforce HTTPS"** under Settings → Pages

## Building locally (optional)

```bash
cd en && quarto render
cd ../nl && quarto render
```

Outputs are written to `_site/en` and `_site/nl` (see `output-dir` in each `_quarto.yml`).
Use `quarto preview` from within a language folder to preview locally before pushing.

## SEO checklist per language version

- [ ] `lang:` correctly set in each `_quarto.yml` (en / nl)
- [ ] `hreflang` alternate tags point to each other (already included in `include-in-header`)
- [ ] `canonical` tag points to the correct language version
- [ ] Separate `site-url` per language so each sitemap.xml generates correctly
- [ ] Content is rewritten per language — not machine-translated 1:1
- [ ] Internal links within a language version stay within that language version

## Next steps

1. Fill in the example blog posts with real content.
2. Add notebooks and slides as content becomes available.
3. Connect your domain via DNS to GitHub Pages (CNAME / A-records).
