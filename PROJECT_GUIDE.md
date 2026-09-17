# MZUNGU Cakes Project Guide

This document explains the MZUNGU Cakes website, its features, its Firebase
backend, and the normal way to manage the site. It is written for anyone who
needs to understand or operate the project without reading all of the code.

## 1. Project Links

- Public website: https://tariq28-code.github.io/mzungu-cakes/
- Admin dashboard: https://tariq28-code.github.io/mzungu-cakes/admin.html
- GitHub repository: https://github.com/Tariq28-code/mzungu-cakes
- Firebase project: `mzungu-cakes`

The website is hosted on GitHub Pages. The customer-facing site is static, but
Firebase provides the database, login, orders, page settings, and analytics.
Catalog images use public URLs, so Firebase Storage and Blaze billing are not
required.

## 2. What The Website Does

Customers can:

- Browse real photos of cakes, cupcakes, cookies, brownies, loaf cakes, and drinks.
- Build a custom cake by choosing an occasion, design direction, shape, flavour, icing colour, and size.
- See an SVG preview and estimated price while building a cake.
- See estimated serving amounts for cake sizes and cupcake quantities.
- Add a cake to the cart and submit an order.
- Choose pickup or delivery and provide a delivery address when needed.
- Choose a date and add notes for the baker.
- Receive an order reference and confirm the order through WhatsApp.
- Order ready-made products directly through WhatsApp.
- Choose a drink category before ordering drinks.
- Choose quantities for ready-made cupcakes, muffins, cookies, brownies, loaf cakes, and drinks before opening WhatsApp.

The custom checkout stores website orders in Firebase and also gives the
customer a WhatsApp confirmation link. Ready-made product links open WhatsApp
directly; they do not create a database order until the customer uses the
custom checkout flow.

## 3. Admin Dashboard

Open the admin dashboard and sign in with the Firebase Authentication user:

https://tariq28-code.github.io/mzungu-cakes/admin.html

The dashboard has four areas:

### Orders

The Orders area shows website checkout orders, including:

- Order reference
- Customer name and phone
- Pickup or delivery information
- Required date
- Products and prices
- Notes
- Total amount
- Current status

Order statuses are `New`, `Confirmed`, `Ready`, and `Completed`.

### Catalog

The Catalog area controls public products, cake-builder occasions and flavours,
shapes, and design directions.

The **Import current menu and flavours** button creates the original built-in
items in Firebase, including four built-in occasions, the built-in shapes, and
three design directions for each occasion. It skips matching records, so it is
safe to use repeatedly during setup.

The admin can then:

- Add products, flavours, occasions, shapes, or design directions.
- Edit existing items.
- Delete items.
- Change names, categories, descriptions, prices, units, and availability.
- Set flavour add-on prices and flavour colours.
- Set shape add-on prices and choose a design occasion/type.
- Set occasion base prices, customer-facing meta text, and availability.
- Upload optional product, flavour, occasion, shape, and design images.
- Preview an item before saving it.

When available occasion records exist, the public website uses them instead of
the built-in occasion data. If Firebase is empty or unavailable,
the website falls back to its built-in content so the public page can still
load.
Cupcake cards use accessible multi-select flavour checkboxes from the active
flavour records, and the choices are included in their WhatsApp request.

### Analytics

Analytics records one aggregate page view per public page load, grouped by UTC
date. It stores only a daily count in `siteAnalytics`; it does not store names,
phone numbers, or visitor-level identifiers.

The Analytics area shows:

- All-time page views
- Today’s page views
- Number of tracked days
- A daily breakdown with simple bar visualizations

Analytics is intentionally basic and privacy-friendly. It is not a unique
visitor count and can count repeat visits or refreshes.

### Page Editor

The Page editor follows the visual language of the public website and includes a
live preview. It controls structured content rather than arbitrary HTML, which
prevents accidental damage to the layout.

The admin can edit:

- Brand name and descriptor
- Hero heading and paragraph
- Business hours
- WhatsApp phone number
- Pickup address
- Menu note
- Three testimonials and customer names
- Footer tagline
- Whether section animations are enabled
- Animation style: fade up, fade, or soft zoom

Click **Save / Publish** to save changes to the `siteSettings/home` Firebase
document. The public website reads this document when it loads and uses default
fallback text if it is missing.

## 4. How The Technology Fits Together

### Public page: `index.html`

This is the complete public website. It contains the HTML, CSS, and JavaScript
in one file so GitHub Pages can host it without a build system.

It contains:

- Layout and styling
- Cake builder data and pricing fallback values
- SVG cake preview generation
- Cart and checkout behavior
- Firebase configuration
- Firestore page-settings and catalog loading
- Daily analytics tracking
- WhatsApp links

### Admin page: `admin.html`

This is a separate protected page. Firebase Email/Password Authentication
controls access. It uses Firestore to read and write orders, catalog records,
page settings, and analytics.

### Firestore

The project uses these collections:

| Collection | Purpose |
|---|---|
| `orders` | Customer checkout orders and statuses |
| `catalog` | Public products and cake-builder flavours |
| `siteSettings` | Editable page content, stored in document `home` |
| `siteAnalytics` | Daily public page-view totals |

### Catalog images

Catalog images are stored as publicly accessible URL strings on each Firestore
`catalog` document. Paste the URL into the admin catalog form. No Firebase
Storage setup or Blaze billing is required.

## 5. Firebase Setup Requirements

The Firebase project must have:

1. A **Cloud Firestore Standard edition** database with database ID `(default)`.
2. Email/Password Authentication enabled.
3. One admin user created under Authentication → Users.
4. The current `firestore.rules` published in Firestore → Rules.

The Firestore database must be Standard edition. Enterprise edition is not
compatible with the browser Firestore setup used by this project.

The Firebase web configuration appears in the public code. This is normal for a
browser Firebase application. Do not put passwords, service-account JSON files,
or private keys in the repository.

## 6. Security Rules

`firestore.rules` controls access as follows:

- Customers can create valid new orders.
- Signed-in admins can read and manage orders.
- Anyone can read public catalog and page settings.
- Signed-in admins can write catalog and page settings.
- Anyone can increment a valid daily analytics record by one view.
- Only signed-in admins can read analytics.

The legacy `storage.rules` file is unused by the application and should not be
published.

Whenever the rules change, publish the complete current files in the Firebase
console. Do not paste only one section into old rules without checking the rest
of the file.

## 7. Deployment

The repository has a GitHub Actions workflow at:

`.github/workflows/pages.yml`

Every push to the `main` branch deploys the site automatically to GitHub Pages.

Normal deployment process:

1. Edit the project files.
2. Run `git diff --check`.
3. Commit the intended changes.
4. Push to `main`.
5. Wait for the `Deploy MZUNGU Cakes to GitHub Pages` workflow to succeed.
6. Refresh the public or admin page with `Ctrl + Shift + R` if an old version is cached.

## 8. Important Placeholder Content

The following content still needs real business confirmation before launch:

- Pickup address and map location
- Business hours
- Menu prices
- Testimonials
- Product availability
- Any remaining placeholder wording

The phone number currently configured is `0759683053`, displayed as
`+255 759 683 053` in international WhatsApp links.

## 9. Troubleshooting

### Orders do not save

- Confirm Firestore is Standard edition.
- Confirm the database ID is `(default)`.
- Confirm the latest Firestore rules are published.
- Confirm the customer is submitting through cart checkout, not only clicking a direct WhatsApp product link.
- Refresh with `Ctrl + Shift + R`.

### Admin shows no orders

- Confirm the order was submitted through the website checkout.
- Sign in with the Firebase Authentication admin user.
- Open Orders and click Refresh.

### Catalog import says permission denied

Publish the latest `firestore.rules`. The admin must be signed in before using
the import button.

### Catalog image does not appear

- Confirm the URL is publicly accessible without signing in.
- Confirm the URL points directly to an image and uses `https://` where possible.
- Confirm the admin is signed in when saving the catalog record.

### Analytics is empty

Open the public website once after publishing the latest rules. Analytics starts
from the first page load after the `siteAnalytics` rules are published.

## 10. Future Improvements

Possible next improvements include:

- Unique visitor analytics and traffic sources through Google Analytics reports.
- Delivery fee configuration.
- Richer admin-managed occasion and design presentation.
- Product quantities and ready-made product orders saved directly in Firestore.
- Customer order notifications by email or WhatsApp automation.
- Image URL validation and broken-image handling.
- Custom domain setup.
