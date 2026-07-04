# ZACMO Theme — Changes Applied to Your Team's Build

This is your uploaded theme (`WEBSITE_TEAM_ZACMO.zip`) — previously built on a
purchased base called **"EYEZY ULTIMATE" by KYXTRO**, with a prior brand's
logo (`CLYRO.png`) still wired into it. Below is exactly what was changed and
why, plus what still needs your input.

## 1. Branding removed
| Where | Before | After |
|---|---|---|
| `config/settings_schema.json` (`theme_info`) | `theme_name: "EYEZY ULTIMATE"`, `theme_author: "KYXTRO"`, kyxtro.com URLs | `"ZACMO"`, zacmo.com placeholders |
| `config/settings_schema.json` (social placeholders) | `facebook.com/kyxtro`, etc. | `facebook.com/zacmo`, etc. |
| `config/settings_data.json` | Header logo + overlay logo pointed at `shopify://shop_images/CLYRO.png` (a different brand's uploaded logo) | Cleared to blank — **you need to upload your real ZACMO logo** in Theme Editor → Header settings |
| `sections/footer.liquid` | Hardcoded `<a href="https://kyxtro.com">SITE BY KYXTRO</a>` credit line + an "eyecey copyright" toggle | Removed entirely (a self-branded store shouldn't credit a third-party template site) |
| `assets/brandname.svg` | Traced wordmark artwork belonging to the old theme demo | Replaced with a clean text-based "ZACMO" SVG wordmark (swap for your real logo file whenever ready) |

**Action needed from you:** upload the ZACMO logo file in
**Theme Editor → Header** and **Footer → Big logo** (if used). Nothing will
silently show old branding — those fields are now blank until you do.

## 2. Splash screen — made session-based
`snippets/preloader.liquid` previously fired on **every page load** with no
session logic. It's been upgraded to:
- Show once per browser **session** via `sessionStorage` (not on every route
  change — closes the loop on your requirement)
- Falls back to showing once per page load only if `sessionStorage` is
  blocked (e.g. strict privacy mode)
- Added a ZACMO red loading bar under the logo
- Click-to-skip support
- Set `enable-pageloader` to `"on"` by default in `settings_data.json` so
  it's active out of the box (toggle it off in Theme Editor → Theme settings
  → Preloader if you don't want it)

## 3. 3D Configurator + Tech Specs — integrated into the real product page
`sections/product-main.liquid` had a working gallery/variant-swatch UI, but
with two real bugs and no 3D/spec support:
- **Bug fixed:** variant swatch clicks only ever matched on option position
  `2` (assumed "color" was always the 2nd option) and never updated price,
  the hidden variant ID, or the Add to Cart button. If a customer picked a
  variant via swatches (not just via one option), the wrong variant could be
  added to cart. The matcher now tracks **all** selected options together.
- **Added:** a `<model-viewer>` 3D viewer, hidden automatically when a
  variant has no 3D model (so products without one behave exactly as
  before)
- **Added:** a live Tech Specs table (GSM, Fabric Quality, Fit, Care) that
  updates the instant a swatch is clicked
- New files: `snippets/tech-specs-table.liquid`, `assets/zacmo-configurator.css`
- `layout/theme.liquid` now loads the `<model-viewer>` web component and the
  new stylesheet

**Action needed from you — set up metafields** (Shopify Admin → Settings →
Custom data → Variants → Add definition):

| Key | Type | Example |
|---|---|---|
| `custom.model_3d` | File reference (.glb) | tshirt-black.glb |
| `custom.model_poster` | File reference (image) | tshirt-black-poster.jpg |
| `custom.gsm` | Number (integer) | 250 |
| `custom.fabric_type` | Single line text | Acid Wash / High-Density Cotton |
| `custom.fit` | Single line text | Regular Fit / Oversized |
| `custom.care` | Multi-line text | Machine wash cold, inside out |

Repeat the same definitions under **Products** (not just Variants) to use as
fallback values when a spec doesn't vary by variant. No model uploaded for a
variant yet? The viewer just stays hidden and the existing 2D gallery is
shown — nothing breaks.

## 4. Account pages restyled
Your theme already used `.account-button` / `.account-input` classes (good
instinct from your dev team) — they just weren't styled with any brand
identity yet (default 1px grey borders, no color). Centralized the styling
into one new file, **`assets/zacmo-account.css`**, applied across:
`login.liquid`, `register.liquid`, `account.liquid`, `addresses.liquid`,
`order.liquid`, `reset_password.liquid`.

Removed the old duplicated inline `<style>` blocks that redefined
`.account-button` / `.account-input` per-template (they would have visually
fought with the new centralized styling since inline styles render after the
linked stylesheet). Page-specific layout rules (e.g. address card grids,
order tables) were left untouched.

Result: black buttons that turn ZACMO red on hover, red focus rings on
inputs, consistent uppercase heading typography site-wide.

## 5. New content sections added
- `sections/product-reviews.liquid` — block-based reviews with star ratings,
  verified-buyer badges, optional customer photos. No app required.
- `sections/fabric-quality.liquid` — icon + copy callouts for GSM/fabric
  explainers.
- Both wired into `templates/product.json` (they now appear on every product
  page, between the buy box and "You may also like"). Fully editable via
  Theme Editor blocks — add/remove reviews and fabric points with no code.

Note on sizing: your team's existing **per-product Size Chart image +
modal** (already in `product-main.liquid`) was left as-is — it's a solid,
already-working per-product approach. If you'd also like a **global,
tabbed** T-shirt vs Full-Sleeve vs Acid-Wash size guide (e.g. for a
standalone Size Guide page), say the word and I'll add it as an additional
section.

## 6. What was intentionally left alone
- `sections/header.liquid`, `sections/enterpage.liquid`, `snippets/cart-drawer.liquid`,
  `snippets/music-player.liquid`, and all other sections/snippets — no legacy
  branding found in these, left untouched to minimize risk to your team's
  existing work.
- Your existing color settings (`config/settings_data.json`) were already
  black/white only — no red/black/white violations found there. Red was
  added only as new accent (hover states, badges, splash loading bar), not
  by changing your team's existing button/background colors.

## 7. Real ZACMO logo wired in
Your uploaded logo (white/red wordmark with the lightning-slash through
"A/M" on black) is now a first-class theme asset, not just a settings
reference you'd have to manually attach:

- Processed into two transparent-background PNGs so it drops cleanly onto
  any surface instead of carrying a black box around it:
  - `assets/zacmo-logo.png` — white lettering + red bolt, transparent
    background. Used wherever the surface is dark: the homepage header
    overlay (`settings.header-overlay-logo` fallback) and the splash screen.
  - `assets/zacmo-logo-dark.png` — same mark with the lettering inverted to
    near-black (bolt stays red), transparent background. Used on the
    standard site header and the footer's optional big logo, both of which
    sit on your white body background.
- `sections/header.liquid`: both the homepage hero-overlay logo and the
  standard (all other pages) logo now fall back to the correct variant
  automatically when no logo is set in Theme Editor — you don't have to
  upload anything for it to show correctly, though you still can override
  either slot with a different file if you want.
- `sections/footer.liquid`: big-logo fallback repointed from the placeholder
  wordmark to `zacmo-logo-dark.png`.
- `snippets/preloader.liquid` (splash screen): logo fallback repointed to
  `zacmo-logo.png`; also set `pageloader-bg` to `#0A0A0A` (black) and
  `preloader-text-color` to white in `settings_data.json` so the splash
  background actually matches the white/red logo instead of the old white
  splash background it would've clashed against.
- Removed `assets/brandname.svg` (the placeholder text wordmark from the
  previous step) — nothing references it anymore.

If you'd like the logo re-cropped, recolored, or want a square/icon-only
mark (e.g. for the favicon or social previews), send the source file and I
can generate those variants too.


## 8. Files changed or added
```
MODIFIED
  config/settings_schema.json
  config/settings_data.json
  layout/theme.liquid
  sections/header.liquid
  sections/footer.liquid
  sections/product-main.liquid
  snippets/preloader.liquid
  templates/product.json
  templates/customers/login.liquid
  templates/customers/register.liquid
  templates/customers/addresses.liquid
  templates/customers/order.liquid
  templates/customers/reset_password.liquid

ADDED
  assets/zacmo-logo.png          (real logo, white/red, transparent — dark surfaces)
  assets/zacmo-logo-dark.png     (real logo, black/red, transparent — light surfaces)
  assets/zacmo-configurator.css
  assets/zacmo-account.css
  snippets/tech-specs-table.liquid
  sections/product-reviews.liquid
  sections/fabric-quality.liquid

REMOVED
  assets/brandname.svg           (placeholder wordmark, superseded by real logo)
```

## How to deploy
Upload this whole folder via **Shopify Admin → Online Store → Themes →
Add theme → Upload zip**, or push it through your existing Shopify
CLI/GitHub workflow if your team manages this theme that way — it's a
drop-in replacement for what you uploaded, not a separate module.
