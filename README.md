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

**No free-trial language until Orla 1.0.1 is live and the App Store Connect
intro offer exists.** Adding it before then would advertise something a
downloader cannot get.

## Deploying

Pushing to `main` publishes. Custom domain and DNS are configured once — see
the deploy notes in `99-logs/`.
