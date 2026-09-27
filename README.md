# SJOS Property Maintenance website

One-page website for [SJOS Property Maintenance](https://www.sjos.ie/), built with [Hugo](https://gohugo.io/) (extended) and deployed to GitHub Pages.

## Run locally

```bash
hugo server            # http://localhost:1313, rebuilds on save
hugo --minify          # production build into ./public
```

## Where things live

| What | Where |
|---|---|
| Home page text, lists, stats | `data/en/homepage.yml` |
| Phone, email, socials, menus, footer, site description | `config.toml` |
| Page templates and sections | `layouts/` (home sections: `layouts/partials/_home-sec-*.html`) |
| Styles | `assets/scss/` (colours and fonts in `vars.sass`) |
| Images, favicons, fonts | `static/` |
| Planning notes, brand source files | `docs/` |
