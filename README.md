# Tweetalige Quarto-site — structuur

## Waarom twee subprojecten in plaats van één?

Quarto heeft geen ingebouwde i18n voor websites. Het gangbare patroon is daarom
**twee losse Quarto-projecten** (`/en` en `/nl`), elk met een eigen `_quarto.yml`,
navbar en `lang:`-instelling. Een `index.html` op de root stuurt bezoekers door
of laat ze kiezen.

```
quarto-site/
├── index.html              ← taalkeuze / redirect naar /en
├── en/
│   ├── _quarto.yml         ← eigen navbar, site-url, hreflang
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

## Bouwen — automatisch via GitHub Actions (aanbevolen)

Er staat al een workflow klaar op `.github/workflows/publish.yml`. Deze:

1. Rendert `en/` en `nl/` apart met Quarto
2. Verzamelt de output samen met `index.html` in één `_publish`-map
3. Publiceert die map naar GitHub Pages

**Wat je zelf moet doen om dit te activeren:**

1. In je repo: **Settings → Pages → Source** → kies **"GitHub Actions"**
   (niet "Deploy from a branch" — die optie is voor Jekyll en niet relevant hier)
2. Pas het `CNAME`-bestand in de root aan met jouw eigen domein
   (staat nu op `balyzai.nl`, wijzig dit zodra je naar `trajectorium.ai` overstapt)
3. Commit en push alles naar de `main`-branch — de workflow start automatisch
4. Volg de voortgang onder het tabblad **Actions** in je repo
5. Zodra de eerste run succesvol is, verschijnt bij Settings → Pages ook de
   "Enforce HTTPS"-optie — vink die aan

## Handmatig bouwen (optioneel, voor lokaal testen)

```bash
cd en && quarto render
cd ../nl && quarto render
```

Beide outputs komen terecht in `_site/en` en `_site/nl` (zie `output-dir` in
de `_quarto.yml`'s) — handig om lokaal te previewen (`quarto preview` vanuit
elke taalmap) voordat je pusht.

## SEO-checklist per taalversie

- [ ] `lang:` correct gezet in elke `_quarto.yml` (en/nl)
- [ ] `hreflang` alternate-tags wijzen naar elkaar (al opgenomen in `include-in-header`)
- [ ] `canonical` tag wijst naar de eigen taalversie (niet naar de andere taal)
- [ ] Eigen `site-url` per taal, zodat sitemap.xml per taal correct genereert
- [ ] Content is herschreven per taal (niet 1-op-1 machinevertaald) — andere
      zoekintentie, andere keywords per taal
- [ ] Interne links binnen een taalversie blijven binnen die taalversie

## Vervolgstappen

1. Vervang `yourdomain.com` / `jouwdomein.nl` door je echte domein in beide
   `_quarto.yml`-bestanden.
2. Vul de voorbeeld-blogposts aan met echte content (zie content-plan).
3. Voeg `notebooks/index.qmd` en `slides/index.qmd` toe per taal zodra je
   die content hebt.
4. Koppel je TransIP-domein via DNS aan GitHub Pages (CNAME/A-records).
