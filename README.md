# SJOS Property Maintenance website

One-page website for [SJOS Property Maintenance](https://sjos.ie/), built with [Hugo](https://gohugo.io/) (extended) and deployed to GitHub Pages.

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

## Deployment

Every push to `main` builds the site and deploys it to GitHub Pages (`.github/workflows/hugo.yml`); it can also be run by hand from the **Actions** tab.

- Live URL: https://sjos.ie/ (custom domain set under **Settings → Pages**; `www.sjos.ie` and `salilwalavalkar.github.io/sjos/` redirect to it)
- DNS is at Blacknight: `sjos.ie` has A/AAAA records for GitHub Pages (185.199.108–111.153, 2606:50c0:8000–8003::153) and `www` is a CNAME to `salilwalavalkar.github.io`. Keep the Titan email records (MX, SPF TXT, `mail`).
- Once GitHub has issued the certificate, tick **Enforce HTTPS** and re-run the workflow so the site's canonical URLs become `https://sjos.ie/`.
