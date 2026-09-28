# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A one-page static website for SJOS Property Maintenance (Irish, family-owned property maintenance business), built with Hugo **extended** 0.166 (it uses Hugo Pipes `toCSS` on Sass) and deployed to GitHub Pages at the custom domain `sjos.ie`. It was adapted from a third-party lighting-studio Hugo theme; the MIT notice in `LICENSE` must stay, but the theme should not be named anywhere else.

## Commands

```bash
hugo server --disableFastRender   # dev server on http://localhost:1313 (also writes to public/)
hugo --minify                     # production build into public/

# Reproduce the CI build locally (site served from a sub-folder, relative URLs off):
HUGO_RELATIVEURLS=false hugo --gc --minify --baseURL "http://127.0.0.1:8767/sjos/" -d /tmp/ghp/sjos
(cd /tmp/ghp && python3 -m http.server 8767)   # then open http://127.0.0.1:8767/sjos/
```

There are no tests or linters. Verify changes by loading the page in a browser at desktop and phone (~390px) widths.

If an edit to a template under `layouts/` doesn't show up, restart `hugo server` — its file watcher sometimes misses files saved via a temp-file-and-rename.

## Architecture

**Single page, data-driven.** `layouts/index.html` renders the header plus four home sections, each a partial: `_hero.html`, `_home-sec-about.html` (id `about`), `_home-sec-features.html` ("Why SJOS", id `why-sjos`), `_home-sec-features-gallery.html` ("Who we work for", id `who-we-work-for`). All their text, lists, stats, buttons and icon names come from `data/en/homepage.yml` (loaded via `index .Site.Data .Site.Language.Lang`). Partials render a section/element only when its data key exists, so removing a key from the YAML removes it from the page.

**Site-wide settings live in `config.toml` `[params]`/menus:** phone/email/WhatsApp/SMS contacts (`params.contacts`, also used by the top bar `_topbar.html`, which shows only the `tel` and `msg` entries), social links (`params.soc` → `_soc.html`, used in hero and footer), footer text/tagline/credit (`params.footer`), logo paths, SEO description and `og_image`. Header and footer menus use `#section-id` URLs; `header.html` and `footer.html` output URLs starting with `#` raw (running them through `absLangURL`/`relURL` would turn them into absolute site URLs).

**Icons** are an inline SVG sprite in `layouts/partials/_svg.html` (included at the end of `baseof.html`) and referenced as `<use xlink:href="#id">`. Icon ids are often chosen in data/config (e.g. `icon: i-bolt` in `homepage.yml`, `icon = "wa"` in `config.toml`), so check those before removing a symbol.

**Markdown links** go through `layouts/_default/_markup/render-link.html`, which applies `safeURL` so `tel:`/`sms:` links in `homepage.yml` (`cta` fields, rendered with `markdownify`) aren't replaced with `#ZgotmplZ`; `http` links get `target="_blank"`.

**SEO** is in `layouts/partials/_seo.html`: title, per-page description fallback to `params.description`, canonical, Open Graph/Twitter tags, favicons from `static/fav/`, and LocalBusiness JSON-LD on the home page. `layouts/robots.txt` adds the sitemap URL (`enableRobotsTXT = true`).

**Styles** are Sass (indented syntax, **tab-indented**) in `assets/scss/`, compiled by Hugo Pipes in `head.html` from `main.sass` (imports the partials) and `custom.scss`. Colours, fonts and the `+maw($bp)` max-width media mixin are in `vars.sass`; `$main` (#B95E06) is the brand orange for backgrounds/buttons, `$mainText` (#E07B12) is the orange for text on dark backgrounds (contrast). Section styles are in `_styles.sass` (`.section--*`, `.stat`, `.chip`, `.serve`, `.section-cta`), header/hero/footer in `_nav.sass`, buttons in `_btn.sass`. Many identical `+maw(...)` blocks exist, so anchor edits on nearby unique lines. The Manrope font is self-hosted in `static/fonts/manrope/` (`@font-face` in `fonts.sass`, preloaded in `head.html`).

`layouts/_default/single.html`, `list.html`, `_breadcrumbs.html` and `_single-cta.html` are currently unused (no content pages exist; `content/` is empty) but kept for a possible future Projects page.

## URLs, previews and deployment

- `config.toml` keeps **`relativeURLs = true`**: the owner previews `public/index.html` from a sub-folder (IDE preview), where root-relative URLs break. Do not change this.
- Hugo's relative URLs break when the site is served from a sub-folder, so the GitHub Actions build (`.github/workflows/hugo.yml`, Hugo 0.166.0 extended, runs on push to `main` or manually) sets `HUGO_RELATIVEURLS=false` and uses `--baseURL` from `actions/configure-pages` (`https://sjos.ie/`).
- Links to the home page must use `.Site.Home.RelPermalink` (not `"/" | relURL`) so they work under a sub-folder. Inline `style="background-image:url(...)"` is not rewritten by `relativeURLs`.
- GitHub Pages serves `404.html` at any missing URL; `head.html` adds `<base href="{{ .Site.BaseURL }}">` on the 404 page so its assets resolve.
- The Pages custom domain is `sjos.ie`, served over HTTPS (Let's Encrypt certificate for `sjos.ie` and `www.sjos.ie`, auto-renewed by GitHub) with "Enforce HTTPS" on; `http://` and `www` redirect to `https://sjos.ie/`. DNS is at Blacknight, delegated to `ns1/ns2.blacknightdns.com` and edited in Blacknight's DNS Manager: apex A/AAAA records point at GitHub Pages (`185.199.108–111.153`, `2606:50c0:8000–8003::153`) and `www` is a CNAME to `salilwalavalkar.github.io`. Email is Titan: keep the MX, SPF TXT and `mail` records. If the certificate ever fails to issue or renew, removing and re-adding the custom domain in Settings → Pages makes GitHub request it again.

## Working agreements

- Don't delete images or other assets under `static/` without asking first; back up anything removed.
- After changes, check at desktop and ~390px widths that images/backgrounds load both on `localhost:1313` and when `public/index.html` is opened from a sub-folder.
- `public/`, `resources/_gen/`, `.idea/` and `docs/first-time-setup/` (local planning notes and the design brief) are git-ignored and must not be committed.
