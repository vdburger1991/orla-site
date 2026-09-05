# orla-site

The public marketing page for **Orla**, at `https://orla.tjbventures.ai`.

Static HTML on GitHub Pages — same hosting arrangement as `tjbventures-legal`.
There is no build step: `index.html` is the site. `.nojekyll` tells Pages to
serve the files as-is rather than running them through Jekyll.

## Why it exists

1. **A destination for paid ads.** Meta and Apple Search Ads both work better
   with a real page behind them, and App Store Connect's Marketing URL field
   wants one.
2. **A machine-readable brand source.** AI creative tools (Konvert and the like)
   onboard by scraping a website for colours, type and imagery. This page states
   the real brand rather than making a tool guess: the tokens in `<style>` are
   copied from `DesignSystem/Colors.swift`, and there is Open Graph plus
   `SoftwareApplication` JSON-LD for anything that reads structured data.

## Files

```
index.html          the whole page — content, styles, OG tags, JSON-LD
CNAME               orla.tjbventures.ai
.nojekyll           serve as plain static files
robots.txt          allow all + sitemap pointer
sitemap.xml         single URL
assets/
  orla-icon-plum.svg    app icon (favicon; full-bleed, CSS rounds it)
  orla-mark-*.svg       transparent marks, plum and oat
  orla-avatar-1024.png  apple-touch-icon
  og-image.png          1200x630 social card
  shots/*.png           App Store screenshots, downscaled to 620px wide
```

Brand sources of truth stay in ClaudeOS at
`20-business/marketing/orla/brand/` and `.../appstore-screenshots/final/`.
The copies here are derived; change the source first.

## Copy rules

Every user-facing string is bound by the marketing compliance lexicon in
`20-business/marketing/quality-gates.md` §COPY. Before changing copy, run:

```
python3 20-business/marketing/marketing-lint.py <extracted-copy.md>
```

Specifically: no efficacy or causal claims, no direction words about her
symptoms, no privacy absolutes ("never leaves your device" is false under
CloudKit and RevenueCat), and the disclaimer sentence is reproduced verbatim
from `disclaimer.md` §9.

**Free-trial language is now allowed** (corrected 5 Sep 2026). Orla 1.0.1 is
live and both `orla_pro_monthly` and `orla_pro_annual` carry a ONE_WEEK trial,
verified against RevenueCat on 2 Sep. The page says "7 days free".

**Never state a price without its currency, and never put a US and a GB price
on the same page.** Prices are $9.99/mo // $49.99/yr in the US and £9.99 //
£49.99 in the UK; organic UK visitors reach this page too, so it says "then a
monthly or yearly plan" and defers the number to the App Store.

## Deploying

Pushing to `main` publishes. Custom domain and DNS are configured once — see
the deploy notes in `99-logs/`.

---

## Landing v2 — paid traffic (5 Sep 2026)

Meta refuses an `apps.apple.com` URL as a Traffic destination
(`2026-09-05-meta-traffic-v4.md` §0/§2.1), so every Meta ad now lands here and
this page carries a Meta web pixel. Design canvas for the page:
`99-logs/2026-09-05-orla-landing-v2.md`.

### Two placeholders TJ must replace before this goes live

| Placeholder | Where | Where TJ gets it |
|---|---|---|
| `REPLACE_WITH_PIXEL_ID` | `index.html`, twice — the `PIXEL_ID` constant and the `<noscript>` image | Events Manager → Data sources → Add → Web |
| `REPLACE_WITH_DOMAIN_CODE` | `index.html` `<head>`, `facebook-domain-verification` | Business Settings → Brand safety and suitability → Domains |

While `PIXEL_ID` is the placeholder the page installs a console-logging stub
instead of loading `fbevents.js` — nothing is sent anywhere, and the click
events can still be read in the console.

### URL parameters the page reads

- `?v=<variant>` swaps the headline and subline to match the ad that sent the
  visitor: `347am`, `fourtaps`, `saidvslogged`, `handover`, `default`.
  Anything unrecognised falls back to `default`.
- `?ad=<token>` becomes the App Store `ct=` campaign token (default `meta_lp`).
  Every App Store link on the page is
  `…/app/id6797506794?pt=129257514&ct=<token>&mt=8`.

Both are sanitised to `[a-z0-9_-]`, 40 chars.

### Events

`PageView` and `ViewContent` (`content_name: orla-landing`, plus the variant) on
load; on an App Store tap, `StartTrial` and the custom `AppStoreClick`
(`variant`, `ct`). The handler never calls `preventDefault` — a tapped plain
link is the only path that survives Instagram's in-app browser, and an
auto-redirect is the thing that failed on 5 Sep.

### Consent

US visitors: `dataProcessingOptions(['LDU'], 0, 0)` then `consent grant`, and no
banner. Everyone else (timezone not `America/…`, or `en-GB`): `consent revoke`
before `init`, plus a small non-blocking Accept / Decline bar.

### Apple badge

`assets/badge/appstore-badge-*.svg` are Apple's own files, fetched unaltered
from Apple's Marketing Tools badge API. Do not recolour, redraw or crop them,
and keep the clear space around them.

### QA

`docs/qa-2026-09-05/` holds the captures at 390 and 1280.
