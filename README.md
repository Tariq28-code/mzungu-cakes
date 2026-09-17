# MZUNGU Cakes — Website

A single self-contained website (`index.html`) for MZUNGU Cakes, a custom cake
business in Dar es Salaam. No build step, no backend — open the file in any
browser, or upload it as-is to any static host.

## What's inside

- Hero, "how it works," and an interactive cake builder with a live SVG
  preview and live pricing
- Cart drawer + checkout modal that generates an order reference and a
  pre-filled WhatsApp confirmation link
- A "ready-made treats" menu grid (cupcakes, muffins, cookies, brownies, loaf
  cakes, drinks, special packages) with quantity-aware WhatsApp ordering for
  cupcakes, muffins, cookies, brownies, loaf cakes, and drinks
- Testimonials section
- Footer with address/hours/map, styled to match Soul Treats-style local
  bakery sites in Dar es Salaam

Everything is one HTML file — CSS is in the `<style>` block, JS is in the
`<script>` block at the bottom. Fonts (Fraunces, Cormorant Garamond, Inter)
load from Google Fonts via CDN link tags, so an internet connection is needed
for the fonts to render as designed (the site still works offline, just with
fallback fonts).

## Placeholder content still to replace

Search `index.html` for these and swap in the real values:

| What | Placeholder | Where it appears |
|---|---|---|
| WhatsApp number | `255700000000` | Nav button, footer, checkout confirmation, menu note |
| Instagram handle | `mzungucakes` | Footer link |
| Business hours | `Mon–Sat: 9AM – 7PM · Sun: Closed` | Top bar, footer |
| Pickup address | `Placeholder pickup address, Dar es Salaam, Tanzania` | Footer |
| Map | Generic Dar es Salaam map, no pin | Footer `<iframe>` — replace the `src` with a real Google Maps embed once you have a fixed address |
| Testimonial quotes | 3 made-up quotes | "What customers say" section |
| Ready-made menu prices | Estimated placeholders | "Also on the menu" section |

### Pricing (already real spec values, but confirm before launch)

All in TZS, set near the top of the `<script>` block in the `TYPES`,
`SHAPES`, `FLAVOURS`, and size-multiplier (`SIZES` / `CUPCAKE_SIZES`) arrays:

- Birthday base: 40,000 · Wedding base: 150,000 · Cupcakes/dozen: 28,000 ·
  Custom/Other base: 60,000
- Shape add-ons — Heart: +5,000, Tiered: +35,000
- Flavour add-ons — Red Velvet: +3,000, Fruit Marble: +2,000
- Size multipliers — Small ×1, Medium ×1.7, Large ×2.6

## Open decisions

1. **Order routing** — orders currently only show an on-screen confirmation
   plus a WhatsApp link. Nothing is saved or emailed automatically. Options:
   - Email each order automatically
   - Save each order to a Google Sheet
   - Save each order to a simple database
   - Keep as-is (WhatsApp-only)
2. **Hosting** — a domain you own, GitHub Pages, or keep distributing the
   file directly for now.

## File map

```
mzungu-cakes-project/
├── index.html   ← the whole site
└── README.md    ← this file
```

## Firebase admin orders

Customer checkout orders are saved in Firebase Firestore and can be managed at:

`https://tariq28-code.github.io/mzungu-cakes/admin.html`

Before using the dashboard:

1. Enable Email/Password under Firebase Authentication.
2. Create the admin user under Authentication → Users.
3. Create the Firestore database.
4. Publish `firestore.rules` from the Firebase console (or with the Firebase
   CLI). Firebase Storage and Blaze billing are not required.

The dashboard lets the signed-in admin view orders and change their status to
New, Confirmed, Ready, or Completed. Keep only trusted users in Firebase
Authentication because signed-in users are allowed to manage orders by the
included rules.

## Page editor

After signing in at `admin.html`, the **Page editor** opens first. It has a
main-page-style preview and structured fields for the brand, hero copy, hours,
phone, pickup address, menu note, three testimonials, and footer tagline. Use
**Save / Publish** to write the fields to the Firestore document
`siteSettings/home`. The public `index.html` reads that document on load and
keeps its built-in copy if Firebase is unavailable or the document does not
exist. The editor also controls accessible section transitions with
`animationEnabled` and the whitelisted `animationStyle` values `fade-up`,
`fade`, and `zoom`; the defaults are enabled and `fade-up`. Content is rendered
as text, not arbitrary HTML, and reduced-motion preferences disable transitions.

The **Orders** and **Catalog** navigation items retain the existing order
status controls and catalog editing. Catalog images use publicly accessible URL
text fields, so image management does not require Firebase Storage or Blaze
billing.

## Page-view analytics

The authenticated **Analytics** panel shows aggregate page views from the public
`index.html`. Each page load increments one Firestore document in
`siteAnalytics`, keyed by the UTC date (`YYYY-MM-DD`) and containing only a
`views` count. No names, phone numbers, cookies, IP addresses, or other
visitor-level identifying data are collected. Sign in to `admin.html` and open
**Analytics** to see all-time views, today's UTC total, and the latest daily
breakdown. Publish the included `firestore.rules` before launch so public writes
are limited to valid one-view increments and only authenticated users can read
the aggregates.

### Rules to publish

The included `firestore.rules` intentionally allows public reads of
`siteSettings`, `catalog`, and customer order creation, while authenticated
Firebase users can write site settings and catalog records and manage orders.
Do not remove the `siteSettings` rule or the existing order/catalog rules when
publishing changes. The legacy `storage.rules` file is unused and should not be
published.

## CMS catalog

Open `admin.html` and sign in with the Firebase admin user. The Catalog section
supports structured product, flavour, occasion, cake shape, and design-direction records.
Shapes have a name, add-on price, and optional image. Design directions have a
name, occasion/type, description metadata, and optional image. Products and
Occasions have a customer-facing name, base price, meta/description, availability,
and optional image. Products and flavours retain their existing fields and preview.
Images are stored as publicly accessible URL strings in Firestore `catalog`
documents. Paste an image URL into the admin form; no Firebase Storage or Blaze
billing is required. Public visitors read available records from the Firestore
`catalog` collection; if it has no usable records or cannot be reached, the
hardcoded menu and flavour options remain visible.

Catalog documents use `kind: 'product'`, `'flavour'`, `'occasion'`, `'shape'`, or
`'design'`. Available occasion records replace the built-in builder occasions;
if none exist, the built-in four remain the fallback. The import action seeds
those four occasions idempotently as well as the existing shapes and designs.
Cupcake ordering shows the currently available flavour records as accessible
multi-select checkboxes; the selection is included in the WhatsApp request.
If no flavour is selected, the request asks the bakery to share available
options. Refresh the public page after an admin catalogue change.
The import action also seeds the built-in shapes and design directions without
duplicating matching records. Publish updated Firestore rules after changing
them, and test one public page load plus one authenticated catalog save before
launch.
