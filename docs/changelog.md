# Release Notes

## 1.6.1 (07-18-2025)
- Add passwordless login translations for multiple languages

## 1.6.0 (07-18-2025)
- [CORNERTSONE] PAYPAL-5000 Quick pay buttons are seen on PDP before 'required' option selection (#319)
- [CORNERTSONE] Update to support multiple date fields and remove blank space (#320)
- Optimize quick view functionality by implementing widget caching and improving performance with setTimeout
- Add login with email link feature.

## 1.5.1 (03-14-2025)
- Fix rrp price and sale price display on graphql products
- Fix z-index Paypal button in PDP (#316)

## 1.5.0 (01-20-2025)
- [CORNERSTONE] Add nonce to scripts in checkout and account pages [#2525](https://github.com/bigcommerce/cornerstone/pull/2525)
- [CORNERSTONE] Use fetch when updating variants in cart ([#2521](https://github.com/bigcommerce/cornerstone/pull/2521))

## 1.4.2 (11-08-2024)
- Fix homepage carousel content overflow on Safari
- Fix undefined 'input-font-color' in add payment methods account page

## 1.4.1 (10-04-2024)
- Add option to limit maximum recently viewed products
- delete PayPal test button in data-add-to-cart-wallet-buttons

## 1.4.0 (09-13-2024)
- Fix Quick Search Overlay Issue on Desktop and Menu Display Issue on Mobile When Sticky Header is disabled #299
- Fix whatsapp link #293 (#294)
- Fix top categories on the homepage when no image (#298)
- Update source code from MoonCat 1.8.

## 1.3.4 (08-23-2024)
- Add data-swatches="{{attributeName}}" to product card swatches
- Fix missing loading animation when click on subcategories

## 1.3.3 (07-26-2024)
- Change Twitter icon to X
- Fix Minimum Quantity Purchase & Maximum Quantity Purchase not working on product cards

## 1.3.2 (07-12-2024)
- Correct og:image meta tag, hide images on menu to avoid wrong image detect from social platforms

## 1.3.1 (07-05-2024)
- Fix scroll to top when using pagination on category page.
- Improve og:image tag for home page, category, brand page, blog page. Use store logo as a fallback for pages without og:image.

## 1.3.0 (06-28-2024)
- Hide sidebar on category & brand page when it is empty
- Fix account link missing when signed in
- Fix paginator numbers not working on orders listing page
- Allow unselect option of swatch, rectangle, radio
- Fix sub menu item color not visible on mobile when having only 2-level categories
- Remove background color of the category images

## 1.2.3 (06-14-2024)
- Fix background modal using container fill color (#284)
- Update MoonCat 1.7.4:
  - Fix webfont cannot load Google font with multiple weights
  - Fix NaN of quantity box on product cards
  - Fix default image not showing in search result page for product don't have image thumbnail
  - Fix widget layout not center
  - Fix sort not working at search result page when turn off faceted filter

## 1.2.2 (04-26-2024)
- Fix infinite loading not working when there are 2 filter values selected or select the default sort again

## 1.2.1 (04-18-2024)
- Support html content in the homepage carousel

## 1.2.0 (04-12-2024)
- Release new Game style.
- Remove unused files
- Fix jstree color
- Fix badge sale countdown label #268 (#269)
- Do not hide sale badge on hover card
- Fix show/hide rrp in alsobought product follow theme settings #273 (#274)

## 1.1.2 (03-15-2024)
- Fix out of stock product still display Add to Cart button on PDP
- Fix footer categories limit affect to the main menu #256 (#266)

## 1.1.1 (02-29-2024)
- Display social icon horizontal layout when turn off google map ISSUE-#257 (PR-#258)
- Fix close button on marketing banner #259 (#260)
- Fix color and font weight of price of product card in category page #260 (#261)
- Add config to show/hide customer services in the footer of sub-pages #262 (#263)

## 1.1.0 (01-26-2024)
- remove unused widget heading alignment (#240)
- Fix arrow button on product carousel section #241 (#242)
- Add theme setting to allow hiding compare button on PDP #250 (#251)
- Add category thumbnail size config #252 (#253)
- Add config to turn off category image on mega menu #255 (#256)

## 1.0.1 (12-15-2023)
- Change color variable of the main carousel arrow buttons (#238)
- Fix issue of sub menu open on mobile #126 (#239)

## 1.0.0
- Initial release
