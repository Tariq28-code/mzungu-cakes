# MZUNGU Cakes — Website

A single self-contained website (`index.html`) for MZUNGU Cakes, a custom cake
business in Dar es Salaam. No build step, no backend — open the file in any
browser, or upload it as-is to any static host.

## What's inside

- Hero, "how it works," and an interactive cake builder with a live SVG
  preview and live pricing
- Cart drawer + checkout modal that generates an order reference and a
  pre-filled WhatsApp confirmation link
- A "ready-made treats" menu grid (cupcakes, cookies, brownies, loaf cakes,
  drinks, special packages)
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
4. Publish the rules in `firestore.rules` from the Firebase console.

The dashboard lets the signed-in admin view orders and change their status to
New, Confirmed, Ready, or Completed. Keep only trusted users in Firebase
Authentication because signed-in users are allowed to manage orders by the
included rules.
