# Landing v2 QA — 5 Sep 2026

Captured from `python3 -m http.server` on the `landing-v2` branch with headless
Chrome, each page rendered inside a fixed-width frame so the CSS viewport is
exactly 390 or 1280 (headless `--window-size` alone renders at a different
width and produces misleading captures).

| File | What it shows |
|---|---|
| `mobile-390-fold.png` | 390×844, `?v=347am&ad=meta_v4_347am`, US locale — everything above the fold |
| `mobile-390-fold-default.png` | same, no query parameters — the `default` headline |
| `mobile-390-fold-gb-consent.png` | same, `TZ=Europe/London` — the consent bar appears |
| `mobile-390-sticky.png` | scrolled 1700px — the sticky bottom CTA is on |
| `mobile-390-full.png` | the whole mobile page |
| `desktop-1280-fold.png` | 1280×900 above the fold |
| `desktop-1280.png` | the whole desktop page |

## Measured, not eyeballed

- Cumulative layout shift **0.0000**; every `<img>` carries explicit
  `width`/`height` and `alt`; 6 of 9 images lazy-loaded.
- Transfer: document 31.8 KB, above-the-fold total ~58 KB, whole page with every
  image 113 KB. No frameworks, no webfonts, no external CSS. (Once the real
  pixel id is pasted, Meta's `fbevents.js` is added on top.)
- `?v=` / `?ad=`: `347am` → headline "3:47am. Again."; no params → the default
  headline and `ct=meta_lp`; `?v=<script>&ad="evil x"` → falls back to
  `default` and sanitises to `ct=evilx`.
- All **three** App Store links resolve to
  `https://apps.apple.com/app/id6797506794?pt=129257514&ct=meta_v4_347am&mt=8`.
- Click handler fires, in order,
  `fbq track StartTrial {content_name:"orla-landing",variant:"347am"}` then
  `fbq trackCustom AppStoreClick {variant:"347am",ct:"meta_v4_347am"}`, and does
  not cancel the event — the browser follows the link normally.
- Consent: `TZ=America/New_York` + `en-US` → `needsConsent=false`, bar hidden.
  `TZ=Europe/London` + `en-GB` → `needsConsent=true`, bar shown.
- Sticky CTA: absent at the top, `class="sticky on"` after scrolling 1700px,
  back off when scrolled to the top again.

`mobile-390-sticky.png` needed a capture-time stylesheet neutralising the bar's
transform transition: headless Chrome paints the bar's background but not its
children when the compositing layer is stale. That is a capture artifact, not a
page defect — the bar's geometry and children were measured directly
(y=782.8, h=61.2, w=390, transform identity, badge at x=238.9).

Lighthouse was **not** run: it is not installed on this machine and installing
it was not worth the download. The performance work is structural (inline CSS,
no frameworks, no webfonts, WebP, preloaded hero, lazy below the fold, explicit
dimensions) and the numbers above are measured directly.
