Slim Bundle Builder (Shopify)

A lightweight, theme-drop-in widget that lets customers build a multi-product bundle from a collection with tiered discounts, a live progress bar, a sticky mobile summary/CTA, and a clean Add/Stepper control that switches seamlessly per item.

Features

Tiered pricing (e.g., 5% for 2, 10% for 3, 15% for 4+) with live /ea preview

Progress bar with ticks and “Best Deal” ribbon

Per-item ADD → quantity stepper (±) with matching sizing on mobile & desktop

Sticky mobile summary bar with subtotals, savings and CTA

Desktop scroll list with a floating “Scroll for more scents” hint that auto-hides at the bottom

Cart add posts multiple variants at once and optionally applies discount codes based on tier

Fully namespaced (no theme CSS collisions) and keyboard-accessible

Quick Start

Create a snippet/section

In your Shopify theme, create a new snippet (e.g., snippets/bundle-builder.liquid) or a section (e.g., sections/bundle-builder.liquid).

Paste the full widget code into that file.

Render it on a page

{% render 'bundle-builder' %}


or add the section to a custom page template and assign that template to a page (e.g., “Build a Bundle”).

Pick your collection
In the code, set:

{%- assign scent_collection = collections['frontpage'] -%}


Replace 'frontpage' with your collection handle (e.g., 'candles').

Configuration (Liquid assigns)

At the top of the file you’ll find the main tunables:

{%- assign TR_ID = 'trwidget' -%}           {# unique namespace #}

{%- assign base_unit_price = 93.00 -%}      {# used for /ea previews #}
{%- assign discount_2 = 5 -%}
{%- assign discount_3 = 10 -%}
{%- assign discount_4 = 15 -%}

{%- assign heading_text = 'Pick Your Scents:' -%}
{%- assign add_label = 'Add +' -%}
{%- assign add_to_cart_text = 'Add Bundle to Cart' -%}

{%- assign scent_collection = collections['frontpage'] -%}

{%- assign text_color    = '#434D10' -%}
{%- assign subtext_color = '#6a6a6a' -%}
{%- assign accent_color  = '#2f4f2f' -%}
{%- assign accent_text   = '#ffffff' -%}
{%- assign soft_bg       = '#f3f3f1' -%}
{%- assign card_bg       = '#fbfbfa' -%}
{%- assign border_color  = '#e4e4e0' -%}


The widget reads actual variant prices at runtime for totals. base_unit_price is only for the /ea labels in the tier bar.

Sizing & Layout (single source of truth)

All action sizing (the Add pill and the ± stepper buttons) stays in sync via CSS variables:

.ai-bundle-{{ TR_ID }}{
  --ctrl-h: 35px;     /* height for Add pill AND ± circles */
  --ctrl-fs: 14px;    /* font size for labels/symbols     */
  --ctrl-pad-x: 12px; /* Add pill horizontal padding      */
  --ctrl-gap: 0px;    /* space between − number +         */
  --action-w: 100px;  /* width of the action column        */
}


To make everything smaller or larger, change --ctrl-h (e.g., 32px or 40px).

To make the action column narrower/wider, change --action-w.

The button height and stepper circles are always identical.

Discount Codes (optional)

On Add-to-Cart, the script applies an automatic discount path based on final tier:

2 items → BUNDLE2

3 items → BUNDLE3

4+ items → BUNDLE4

Change the codes here if you prefer different ones:

let code=null; 
if(tier===2) code='BUNDLE2';
if(tier===3) code='BUNDLE3';
if(tier>=4) code='BUNDLE4';


If you don’t use discount URLs, set code=null or remove that redirect and just go to /cart.

How it Works

Each product row switches between the Add button and a Stepper when selected.

Quantities are tracked in a Map(variantId → qty).

Totals, savings, reward text, and the tier progress bar update on every change.

Desktop list: becomes a scrollport with a floating “Scroll for more scents” chip that hides when you reach the bottom.

Mobile summary: sticky bar with totals and CTA that respects safe-area insets.

Accessibility

Buttons use real <button> elements with clear labels.

Visible focus is preserved via :focus-visible.

Stepper controls have aria-label="Increase/Decrease".

Hidden elements use [hidden]{display:none !important;} to avoid AT confusion.

Theming Tips

Update the color tokens at the top for your brand.

Typography inherits from your theme; increase card title size or weight via:

.ai-bundle__title-{{ TR_ID }}{ font-size:15px; font-weight:800; }


Card and list spacing are controlled by the .ai-bundle__item-* padding and the grid gap.

File Structure (suggested)
/snippets
  bundle-builder.liquid         # this widget (or use /sections)


No external assets required.

Browser Support

Modern evergreen browsers (Chromium, Firefox, Safari). Uses only vanilla JS and standard CSS; no build step.

Troubleshooting

“Add” is taller than “±”
Ensure the normalization block is present (it is included in this README version):

.ai-bundle-{{ TR_ID }} .ai-bundle__add-{{ TR_ID }},
.ai-bundle-{{ TR_ID }} .ai-bundle__stepbtn-{{ TR_ID }}{
  -webkit-appearance:none; appearance:none;
  margin:0; border:0; box-sizing:border-box;
  height:var(--ctrl-h);
  font-size:var(--ctrl-fs); font-weight:800;
}
.ai-bundle__add-{{ TR_ID }}{ line-height:var(--ctrl-h); }


Discount code not applying
Check your code names exist in Shopify Discounts. If you use automatic discounts or scripts, remove the redirect logic.

No products appear
Confirm scent_collection uses a valid collection handle and that products are available on the Online Store channel.

Contributing

PRs welcome! Please keep:

Namespace selectors ({{ TR_ID }}) intact.

No external dependencies.

Accessibility and keyboard support.
