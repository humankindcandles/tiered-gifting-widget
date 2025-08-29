# Slim Bundle Builder (Shopify)

A lightweight, theme‑drop‑in widget that lets customers build a multi‑product bundle from a collection with tiered discounts, a live progress bar, a sticky mobile summary/CTA, and a clean Add/Stepper control that swaps seamlessly per item.

> Built to be copy‑paste friendly: one section file with HTML + CSS + JS + Liquid variables at the top for easy theming.

---

## What’s inside

- **Tiered pricing** (e.g., 5% for 2, 10% for 3, 15% for 4+) with live `/ea` preview  
- **Progress bar** with ticks and “Best Deal” ribbon  
- **Per‑item** **ADD → quantity stepper (±)** with **matching sizing** on mobile & desktop  
- **Sticky mobile summary bar** with subtotals, savings and CTA  
- **Redirect with discount code** after add, or keep on page (configurable)  
- Fully **namespaced** to avoid theme CSS collisions

---

## Quick start (Shopify)

> Works as a theme section. You can also paste it into a **Custom Liquid** section on a page to test.

1. **Online Store → Themes → … → Edit code**  
2. Add section → Custom liquid 
3. Paste the file contents from **`build-a-bundle-bar.html`** in this repo into the new section and **Save**.  
4. **Customize** your theme, add the custom liquid to the desired section, and publish.

---

## Configure it (one place)

At the **very top** of the file you’ll see a small Liquid “settings” block. Change values here to make it yours.

```liquid
{%- assign TR_ID = 'trwidget' -%}

{%- assign base_unit_price = 93.00 -%} 
{%- assign discount_2 = 5 -%}
{%- assign discount_3 = 10 -%}
{%- assign discount_4 = 15 -%}
{%- assign heading_text = 'Pick Your Scents:' -%}
{%- assign add_label = 'Add +' -%}
{%- assign add_to_cart_text = 'Add Bundle to Cart' -%}

{%- assign scent_collection = collections['frontpage'] -%}   {# ← change to your collection handle #}

{%- assign text_color    = '#434D10' -%}
{%- assign subtext_color = '#6a6a6a' -%}
{%- assign accent_color  = '#2f4f2f' -%}
{%- assign accent_text   = '#ffffff' -%}
{%- assign soft_bg       = '#f3f3f1' -%}
{%- assign card_bg       = '#fbfbfa' -%}
{%- assign border_color  = '#e4e4e0' -%}
```

### 1) Choose the collection
- Replace `collections['frontpage']` with your **collection handle**, e.g.  
  `collections['candles']` or `collections['slim-bundle']`.
- Tip: In Shopify admin, open the collection and look at the URL to find the handle.

### 2) Discounts & price preview
- `base_unit_price` is used only for the `/ea` **preview** in the tier bar. Set this to your typical product price.  
- Set the 3 tier discounts with integers:  
  - `discount_2` → % off when **2+** items are in the bundle  
  - `discount_3` → % off when **3+** items are in the bundle  
  - `discount_4` → % off when **4+** items are in the bundle

> Discounts are **applied in JS** to the bundle total at checkout time via an automatic **redirect to a discount code** (see below). If you prefer to handle discounts with Shopify Scripts/Functions or automatic discounts, see **“Change the checkout behavior”**.

### 3) Text & labels
- `heading_text` → the small heading over the list  
- `add_label` → label for the per‑item add button (e.g., “Add +”)  
- `add_to_cart_text` → main CTA text (e.g., “Add Bundle to Cart”)

### 4) Colours
Set brand colours once here:
- `accent_color` & `accent_text` control the main green pill buttons and text colour.  
- `text_color`, `subtext_color` affect titles and descriptions.  
- `soft_bg`, `card_bg`, `border_color` theme the cards and containers.

### 5) Exact sizing: **Add** and **±** always match
All sizing is driven by CSS variables and **shared** across both controls so they’re identical.

In the `.ai-bundle-{{ TR_ID }}` rule (already in the file), tune:

```css
.ai-bundle-{{ TR_ID }}{
  --ctrl-h: 35px;     /* height of Add pill AND ± circles */
  --ctrl-fs: 14px;    /* text/symbol size */
  --ctrl-pad-x: 12px; /* Add pill horizontal padding */
  --ctrl-gap: 0px;    /* spacing between − number + */
  --action-w: 100px;  /* width of the action column in each card */
}
```

- Want smaller controls everywhere? Lower `--ctrl-h` (e.g., `32px`).  
- Need more/less width on desktop? Change `--action-w` (e.g., `140px`).  
- The **stepper and add button use the same variables**, so they’ll stay in sync.

> The code also normalizes native button metrics (`appearance:none; margin:0; line-height`) so the “Add +” pill and the ± circles are **pixel‑exact** matches.

---

## Change the checkout behavior

By default, after adding items, we **redirect** with a matching **discount code**:
```js
let code=null; if(tier===2) code='BUNDLE2'; if(tier===3) code='BUNDLE3'; if(tier>=4) code='BUNDLE4';
window.location.href = code ? `/discount/${encodeURIComponent(code)}?redirect=/cart` : '/cart';
```

### Option A — Keep customer on the page
Replace the redirect line with:
```js
// Stay on page (no redirect)
console.log('Added to cart', items);
// Optionally open your theme drawer here if available:
document.dispatchEvent(new CustomEvent('cart:refresh'));
```
*(You’ll need to hook into your theme’s cart drawer event if it has one.)*

### Option B — Use your own discount names
Update the mapping above, e.g.:
```js
if(tier===2) code='MYBUNDLE-5';
if(tier===3) code='MYBUNDLE-10';
if(tier>=4) code='MYBUNDLE-15';
```

### Option C — Handle discounts elsewhere
- Remove the code mapping entirely and just `window.location.href = '/cart'`.  
- Implement discounts via Shopify **Functions/automatic discounts** as you prefer.

---

## Reward copy (change the text shown under the summary)
Inside the script there’s a small map you can edit:
```js
const rewards={
  1:"Free Shipping",
  2:"Free Shipping + 5% Off",
  3:"Free Shipping + 10% Off + Wick Cutter & Snuffer",
  4:"Free Shipping + 15% Off + Wick Cutter & Snuffer + Silk Scarf"
};
```
Change these strings to match your tiers and perks.

---

## How the tier math works

- We track selected quantities per variant and compute the **bundle total**.  
- Discount % is chosen by the **total quantity** across items:  
  - 1 → 0%  
  - 2 → `discount_2`  
  - 3 → `discount_3`  
  - 4+ → `discount_4`  
- The **progress bar** and `/ea` prices in the tier header update live.

---

## Troubleshooting

**“Add + and ± are different heights”**  
- Check the variables in `.ai-bundle-{{ TR_ID }}` and ensure `--ctrl-h` is the same for both.  
- Make sure you did not override `line-height` or `padding` on buttons elsewhere. The section normalizes buttons with `appearance:none; margin:0; box-sizing:border-box; line-height: var(--ctrl-h);` on the add pill and `line-height:1` on the ± to avoid vertical drift.

**Theme styles leaking in**  
- Everything is namespaced with `.ai-bundle-{{ TR_ID }}`. If your theme is very aggressive, keep this section **below** large global component imports, or add `!important` to the provided variables block.

**Wrong collection**  
- Verify the collection **handle**. If the handle is wrong or the collection is empty, the widget will render the “No scents found” fallback.

**Currency**  
- Displayed currency uses `{{ shop.currency }}` and Shopify’s cents‑based prices for items.

---

## File map

- `build-a-bundle-bar.html` — the section code (HTML + CSS + JS + Liquid variables)
- `README.md` — this file
- `LICENSE` — MIT

---

## License

Released under the [MIT License](LICENSE).

Copyright (c) 2025 Humankind Candles
