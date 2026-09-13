# Copilot Instructions — Trajectorium Site

## Project overview
Quarto-based bilingual website (English + Dutch) deployed to GitHub Pages at `trajectorium.ai`.
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
  robots.txt          ← allow all crawlers + sitemap references
  en/                 ← compiled EN site (includes sitemap.xml)
  nl/                 ← compiled NL site (includes sitemap.xml)
  images/             ← shared images (cp -r images _publish/images)
```
If new shared asset folders are added to the repo root, add them here too.

## Rule: blog post SEO frontmatter
Every new blog post (EN and NL) must include the following in the YAML frontmatter, in this order:

```yaml
---
title: "..."
description: "..."          # 1-2 sentences, used as OG description and search snippet
date: YYYY-MM-DD
author: "Jan Scholtes"
image: "https://trajectorium.ai/images/<filename>"   # optional but recommended; use absolute URL
categories: [category1, category2]
lang: en                    # or nl
---
```

After the YAML frontmatter, include the image (if present) and immediately after it the reading time estimate:

```markdown
![](https://trajectorium.ai/images/<filename>){fig-align="center" style="max-width:720px;width:100%;margin:1.5rem 0;"}

*Estimated reading time: X minutes*   <!-- EN -->
*Geschatte leestijd: X minuten*       <!-- NL -->
```

Calculate reading time at 200 words per minute, rounded up to the nearest minute.

- **`author`**: always `"Jan Scholtes"` — required for E-E-A-T signal and author-page aggregation.
- **`image`**: absolute URL only (`https://trajectorium.ai/images/...`). Never relative paths — Quarto builds EN and NL as separate projects and relative paths break the listing and Open Graph previews. Omit the field if no matching image exists.
- **`description`**: written to work as both meta description and Open Graph description. First 160 characters must be self-contained.
- **`categories`**: use existing categories where possible (`fundamentals`, `governance`, `text-to-action`, `sovereignty-edge`, `enterprise-strategy`, `education`, `efficiency`) to keep the taxonomy consistent.

## Rule: Key papers section
Every blog post must include a `**Key papers**` / `**Kernpublicaties**` section immediately after the closing italic source line and before the horizontal rule that introduces "Further reading". Format:

```markdown
*Based on "..." (Prof. dr. ir. J.C. Scholtes, ...).*

**Key papers**

- Author et al. (Year), *Paper Title* — [arXiv:XXXX.XXXXX](https://arxiv.org/abs/XXXX.XXXXX)
- Author et al. (Year), *Paper Title* — [DOI or journal link](https://...)

---

**Further reading on this site**
...
```

- 3-5 papers per post, most seminal or directly cited first.
- Always link to arXiv, DOI, or the original publisher — never to blog posts or news articles.
- Papers in italics, links inline with the paper reference.
- NL posts use `**Kernpublicaties**` with identical formatting.
- **This section is mandatory for every new blog post, including short posts.** Use arXiv where possible; always verify paper titles before publishing.

## Rule: styling
- Theme file: `custom.scss` at repo root.
- Both `_quarto.yml` files reference it as `../custom.scss` under `format.html.theme`.
- Always update `custom.scss` for visual changes; never add inline `<style>` blocks.

## Rule: no emoji
Never use emoji or emoticons in any `.qmd` content file, page titles, navigation labels, card headings, or button text. Plain text and Unicode symbols (→) are allowed.

## Rule: no em-dashes
Never use em-dashes (`—`, U+2014) or their HTML entity (`&mdash;`) anywhere in site content. This includes `.qmd` files, `.md` files, `.yml` config, and `index.html`. Em-dashes are a strong signal of AI-generated text and undermine the site's credibility.

**Instead, use:**
- **Comma** for parenthetical inserts: "The model, trained on our data, works well."
- **Colon** for introducing lists or explanations: "The reason is simple: cost."
- **Period** for hard stops: "The model works. What matters is training data."
- **Parentheses** for asides: "The result (unsurprising) was a failure."

**En-dashes (`–`, U+2013) are allowed** for number ranges (2020–2024, pages 50–100).

## Rule: consistent styling
All new pages must follow the same layout patterns as the homepage: use CSS classes from `custom.scss` (`.feature-card`, `.hero-banner`, `.diagram-section`, etc.). Never add page-specific inline `<style>` blocks or override styles outside `custom.scss`.

## Rule: blog cross-linking
Every new blog post must be cross-linked with existing posts at publication time. Never publish a post in isolation.

**When publishing a new post:**
1. Add inline links within the new post's text wherever it references a concept already covered in another post (e.g. "as covered earlier on this site" becomes an actual link).
2. Add a **"Further reading on this site" / "Verder lezen op deze site"** section at the end of the new post with 3–6 bullet links to the most relevant existing posts, each with a one-line description.
3. Add a link back to the new post in the "Further reading" section of the 2–4 most thematically related existing posts (EN + NL simultaneously).

**Structural consistency:** Every blog post must end with:
- A horizontal rule (`---`)
- A `**Further reading on this site**` / `**Verder lezen op deze site**` heading
- 3–6 bullet links with one-line descriptions

**NL cross-link filenames:** Always use the NL filename in NL posts (e.g. `de-institutionele-slotgracht.qmd`, not `the-institutional-moat.qmd`).

## Rule: NL translation of technical terms
When writing Dutch (NL) translations of blog posts or other content, keep established AI and NLP technical terms in English and render them in *italics*. Do not force Dutch translations for terms that would read as unnatural or pedantic. Examples of terms to keep in English (italicised):

*fine-tuned*, *fine-tuning*, *graph* (in computational context), *transformer*, *token*, *tokenization*, *embedding*, *attention*, *self-attention*, *pipeline*, *chunk*, *chunking*, *reranker*, *reranking*, *RAG*, *guardrails*, *hallucination*, *gradient*, *bottleneck*, *benchmark*, *corpus*, *SFT*, *DPO*, *RLHF*, *preference learning*, *instruction-following*, *training signal*, *reinforcement learning*, *OCR*, *critic agent*, *deduplicated*

Dutch translations are fine for general concepts: *taalmodel* (language model), *geheugen* (memory), *redenering* (reasoning), *laag/lagen* (layer/layers), *beoordeling* (assessment), *bestuur* (governance).

## Rule: project documentation language
All project documentation (README, copilot-instructions, workflow comments, `.qmd` front matter comments) must be written in English.

## Rule: page layout
- Homepage (`index.qmd`): `page-layout: full`, `toc: false`
- Content pages (blog, notebooks, slides): default layout with TOC

## Rule: git workflow
- Branch: `main` — push directly, no PRs needed for content changes.
- Commit messages: concise, imperative, e.g. `Add: NL blog post on RAG`.
- **After every change: always `git add`, `git commit`, `git push` immediately.** Never leave changes uncommitted.
- After pushing, the GitHub Action rebuilds the site automatically. No manual rebuild is needed.

## Domain & config
- Live URL: `https://trajectorium.ai`
- EN site URL: `https://trajectorium.ai/en`
- NL site URL: `https://trajectorium.ai/nl`
- CNAME: `trajectorium.ai`
