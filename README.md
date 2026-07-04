# ZACMO Shopify Theme Module — Integration Guide

## 1. Where files go
Drop these folders directly into a fresh Shopify theme (e.g. Dawn) via
**Shopify Admin → Online Store → Themes → Edit Code**, or via the Shopify CLI /
GitHub integration. Files merge by path — no renaming needed:

```
layout/theme.liquid              -> replace or merge with existing layout
config/settings_schema.json      -> merge the arrays with your existing schema
assets/*.css, assets/*.js        -> add as new files
sections/*.liquid                -> add as new files
snippets/*.liquid                -> add as new files
templates/product.json           -> replace or merge with your product template
templates/customers/*.liquid     -> add as new files
```

If your base theme already has a `header`/`footer` section (most do), the
`{% section 'header' %}` / `{% section 'footer' %}` calls in `theme.liquid`
will just use them — no change needed there.

## 2. Set up the 3D + spec metafields (one-time, in Shopify Admin)
Go to **Settings → Custom data → Variants → Add definition** and create:

| Namespace.key            | Type                  | Example value            |
|---------------------------|-----------------------|---------------------------|
| `custom.model_3d`         | File (accept .glb)    | tshirt-black.glb          |
| `custom.model_poster`     | File (image)          | tshirt-black-poster.jpg   |
| `custom.gsm`               | Number (integer)     | 250                        |
| `custom.fabric_type`       | Single line text     | Acid Wash / High-Density Cotton |
| `custom.fit`               | Single line text     | Regular Fit / Oversized   |
| `custom.care`              | Multi-line text      | Machine wash cold, inside out |

Repeat the same definitions at the **product** level (Settings → Custom data →
Products) to use as fallback values for options that don't vary by variant
(e.g. a T-shirt that's always 250 GSM regardless of color).

Then, on each product's variant edit screen, upload the `.glb` 3D model for
that specific color/style and fill in the spec fields. Free/low-cost tools
like Blender, or a 3D scanning service, can produce web-ready `.glb` files —
keep them under ~5–8MB and Draco-compressed for fast loading.

**No model uploaded for a variant?** The viewer automatically falls back to
that variant's 2D product image — nothing breaks.

## 3. Splash screen
The splash section is added automatically at the top of `theme.liquid`. In
the Theme Editor, open **Sections → Splash Screen** to:
- Upload your logo (auto-rendered in white)
- Set minimum display duration
- Toggle "always show" for merchant previewing (bypasses the session check)

It fires once per browser **session** via `sessionStorage` — a customer
won't see it again until they close and reopen their browser tab.

## 4. 3D Configurator section
Already wired into `templates/product.json`. To add it to a different
template, add a section of type `product-configurator` anywhere in that
template's JSON.

## 5. Reviews / Size Guide / Fabric Quality
All three are block-based sections — add reviews, size chart rows, and
fabric callouts directly in the Theme Editor with no code changes. Size
guide rows use a simple `Size,Chest,Length,Sleeve` per-line format.

## 6. Account pages
`account-button` and `account-input` classes are defined in `assets/account.css`
and used throughout `templates/customers/login.liquid`, `register.liquid`,
and `account.liquid`. If your base theme already renders account pages via
a different template name (e.g. `customers/account.classic.liquid`), rename
accordingly or set these as the default customer templates in
**Online Store → Themes → Customize → Templates**.

Note: `account.liquid` references a `pagination` snippet for order history —
use your base theme's existing pagination snippet, or remove that include if
you don't need paginated orders.

## 7. Brand color enforcement
All color values are centralized as CSS variables in `assets/theme.css`,
sourced from `settings_schema.json` (Brand Colors group). To guarantee strict
red/black/white usage, avoid adding new hardcoded hex values elsewhere —
always reference `var(--zacmo-red)`, `var(--zacmo-black)`, `var(--zacmo-white)`.

## 8. Performance notes
- `model-viewer` loads as an ES module from a CDN and lazy-instantiates;
  it doesn't block first paint.
- Keep `.glb` files compressed (Draco/meshopt) — this is the single
  biggest factor in 3D viewer load speed.
- All CSS uses `clamp()` and CSS Grid for fluid responsiveness — no
  device-specific breakpoints beyond the two included (`989px`, `599px`)
  are needed for most layouts.
- JS is vanilla (no framework dependency), scoped per-section via ID, and
  re-initializes correctly inside the Shopify theme editor
  (`shopify:section:load`).
