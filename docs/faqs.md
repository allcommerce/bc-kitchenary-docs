# FAQs

## Display the product image on the left column on PDP

Q: On the Product page, flip the product image so that its on the left and the description on the right. (Most US sites are like this)

A:

Go to **Storefront** > **Script Manager**, click **Create a Script**, choose:

- **Location on page** = `Head`
- **Select pages where script will be added** = `All Pages`
- **Script type** = `Script`

Enter the script below to **Scripts contents**:


```html
<script>
(function() {
var style = document.createElement('style');
style.innerHTML = '@media (min-width: 801px) {'
+ '.productView-details { float: right; clear: right; padding-left: 1.5rem; padding-right: 0 }'
+ '.productView-images, .productView-alsoBought--right { float: left; clear: left; padding-left: 0; padding-right: 1.5rem }'
+ '}';
document.head.appendChild(style);
})();
</script>
```

## Cookie consent banner covers the CHECKOUT NOW button

Q: With the BigCommerce Cookie Notification switched on, a shopper who has not answered the banner yet can add a product to the cart, but the **CHECKOUT NOW** button in the slide-out cart is hidden behind the banner. Scrolling the cart does not reveal it.

A:

The checkout button sits in a footer pinned to the bottom of the screen, outside the cart's scrolling area, which is where BigCommerce also pins the cookie banner. Scrolling can never reveal the button. The same applies to the sticky Add to Cart bar on product pages, and to desktop as well as mobile.

**This is fixed in the theme itself from the next release.** Use the script below only if you are on an older version and need the fix today. It detects the theme fix and stands itself down automatically, so it is safe to leave in place during an upgrade — though you should delete it once you have upgraded.

Go to **Storefront** > **Script Manager**, click **Create a Script**, choose:

- **Location on page** = `Footer`
- **Select pages where script will be added** = `All Pages`
- **Script category** = `Essential`
- **Script type** = `Script`

**The Script category must be `Essential`.** Scripts filed under Analytics, Functional or Targeting Advertising are withheld by the consent manager until the shopper agrees to cookies. This fix only matters *before* they answer the banner, so any other category means it never runs when it is needed. The script sets no cookies and collects nothing, so Essential is the correct classification.

Script Manager only accepts script code, so the script adds the styles itself.

Enter the script below to **Scripts contents**:

```html
<script>
(function () {
  var css = [
    "  /* Mount signal. BigCommerce injects #consent-manager long after this script",
    "     runs, so this animation makes the browser fire `animationstart` the moment",
    "     the element enters the document. outline-style defaults to none, so nothing",
    "     is ever painted. */",
    "  @keyframes bcConsentBannerMounted {",
    "    from { outline-color: rgba(0, 0, 0, 0); }",
    "    to { outline-color: rgba(0, 0, 0, 0.01); }",
    "  }",
    "",
    "  #consent-manager {",
    "    animation: bcConsentBannerMounted 0.01s;",
    "  }",
    "",
    "  /* The drawer's checkout footer is pinned to the viewport bottom and sits",
    "     outside the scroll area, so it must end above the banner. */",
    "  #cart-preview-dropdown {",
    "    bottom: var(--bc-consent-height, 0) !important;",
    "    transition: right 0.2s ease, bottom 0.2s ease, 0.2s visibility 0.1s;",
    "  }",
    "",
    "  #cart-preview-dropdown.is-open {",
    "    transition: right 0.2s ease, bottom 0.2s ease;",
    "  }",
    "",
    "  /* Sticky add-to-cart bar on product pages, mobile only. */",
    "  @media (max-width: 800px) {",
    "    .productView-qtyAddWrapper._sticky {",
    "      bottom: var(--bc-consent-height, 0) !important;",
    "    }",
    "  }",
    "",
    "  /* Floating widgets such as Smile.io Rewards or a chat launcher. Harmless if",
    "     you do not have them installed. */",
    "  body .smile-launcher-frame-container,",
    "  body .smile-launcher-frame-container.smile-launcher-border-radius-circular,",
    "  body #fc_frame {",
    "    /* --bc-bottom-chrome covers the banner AND the sticky add-to-cart bar,",
    "       which stack on each other on product pages. Clearing only the banner",
    "       would drop the widget straight onto the Add to Cart button. */",
    "    bottom: calc(var(--bc-bottom-chrome, 0px) + 16px) !important;",
    "  }",
    "",
    "  /* Keep the floating widgets off the cart drawer entirely. */",
    "  body.has-previewCartOpened .smile-launcher-frame-container,",
    "  body.has-previewCartOpened .smile-launcher-frame-container.smile-launcher-border-radius-circular,",
    "  body.has-previewCartOpened #fc_frame {",
    "    display: none !important;",
    "  }"
  ].join('\n');
  var s = document.createElement('style');
  s.textContent = css;
  document.head.appendChild(s);
}());

(function () {
    'use strict';

    if (window.__bcConsentBannerOffset) return;
    window.__bcConsentBannerOffset = true;

    var CUSTOM_PROPERTY = '--bc-consent-height';
    var CHROME_PROPERTY = '--bc-bottom-chrome';
    var BANNER_SELECTOR = '#consent-manager';
    var STICKY_SELECTOR = '.productView-qtyAddWrapper._sticky';
    var MOUNT_ANIMATION = 'bcConsentBannerMounted';
    var CONSENT_ANSWERED_EVENT = 'consent_permissions_changed';
    var MAX_VIEWPORT_SHARE = 0.6;
    var BOTTOM_ANCHOR_TOLERANCE = 24;

    var root = document.documentElement;
    var resizeObserver = null;
    var observed = null;
    var pending = false;
    var published = null;
    var publishedChrome = null;

    function measureOverlap(el, allowance) {
        if (!el) return 0;

        var style = window.getComputedStyle(el);

        if (style.display === 'none' || style.visibility === 'hidden') return 0;
        if (style.position !== 'fixed' && style.position !== 'sticky') return 0;

        var rect = el.getBoundingClientRect();

        if (rect.height === 0) return 0;

        var slack = (allowance || 0) + BOTTOM_ANCHOR_TOLERANCE;

        if (rect.bottom < window.innerHeight - slack) return 0;

        var overlap = window.innerHeight - rect.top;

        return Math.max(0, Math.min(overlap, window.innerHeight * MAX_VIEWPORT_SHARE));
    }

    function publish() {
        var value = Math.round(measureOverlap(document.querySelector(BANNER_SELECTOR)));

        if (value !== published) {
            published = value;
            root.style.setProperty(CUSTOM_PROPERTY, value + 'px');
        }

        var chrome = Math.max(value, Math.round(measureOverlap(document.querySelector(STICKY_SELECTOR), value)));

        if (chrome !== publishedChrome) {
            publishedChrome = chrome;
            root.style.setProperty(CHROME_PROPERTY, chrome + 'px');
        }
    }

    function schedule() {
        if (pending) return;
        pending = true;

        var run = function () {
            pending = false;
            publish();
        };

        if (window.requestAnimationFrame) {
            window.requestAnimationFrame(run);
        } else {
            window.setTimeout(run, 16);
        }
    }

    function track() {
        var el = document.querySelector(BANNER_SELECTOR);

        if (el !== observed) {
            if (resizeObserver) resizeObserver.disconnect();

            observed = el;

            if (el && window.ResizeObserver) {
                resizeObserver = new window.ResizeObserver(schedule);
                resizeObserver.observe(el);
            }
        }

        schedule();
    }

    function start() {
        /* Stand down if the theme already ships the permanent fix. */
        if (window.__bcConsentOffset) return;

        document.addEventListener('animationstart', function (event) {
            if (event.animationName === MOUNT_ANIMATION) track();
        }, true);

        /* The banner is removed a beat after the shopper answers it. */
        document.addEventListener(CONSENT_ANSWERED_EVENT, function () {
            track();
            window.setTimeout(track, 300);
        });

        window.addEventListener('resize', schedule);
        window.addEventListener('orientationchange', schedule);
        window.addEventListener('scroll', schedule, { passive: true });
        window.addEventListener('load', track);

        track();
    }

    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', function () { window.setTimeout(start, 0); });
    } else {
        window.setTimeout(start, 0);
    }
}());
</script>
```

### How to check it worked

1. Open a product page on your phone in a private window, so the cookie banner appears.
2. Add the product to the cart without answering the banner.
3. The **CHECKOUT NOW** button should be fully visible, sitting directly above the banner.
4. Tap **Accept All Cookies** or **Reject all**. The banner disappears and the cart expands to the full screen height with no leftover gap.

Test the same path on desktop, and try **Settings** as well — the cookie preferences dialog must still open on top of the cart, not behind it.

## Display mega menu with 4 columns

Go to **Storefront** > **Script Manager**, click **Create a Script**, choose:

- **Location on page** = `Footer`
- **Select pages where script will be added** = `All Pages`
- **Script type** = `Script`

Enter the script below to **Scripts contents**:

@media (min-width: 801px) {
    .navPage-subMenu-list { grid-template-columns: repeat(4, minmax(max-content,350px)) }
    .navPage-subMenu-item:nth-child(3n) { border-right: 1px solid #d6d6d6 }
    .navPage-subMenu-item:nth-child(4n) { border-right: 0 }
}

```html
<script>
(function() {
var style = document.createElement('style');
style.innerHTML = '@media (min-width: 801px) {'
+ '.navPage-subMenu-list { grid-template-columns: repeat(4, minmax(max-content,350px)) }'
+ '.navPage-subMenu-item:nth-child(3n) { border-right: 1px solid #d6d6d6 }'
+ '.navPage-subMenu-item:nth-child(4n) { border-right: 0 }'
+ '}'
;document.head.appendChild(style);
})();
</script>
```

## YouTube Video Customization Guide for BigCommerce Theme

### Overview

This document provides instructions on how to hide video titles and suggested videos in YouTube video embeds for the BigCommerce Kitchenary theme.

### Required YouTube Parameters

To hide titles and suggested videos, add the following parameters to YouTube URLs:

| Parameter | Value | Description |
|-----------|-------|-------------|
| `rel` | 0 | Hide suggested videos when video ends |
| `showinfo` | 0 | Hide video title and information |
| `modestbranding` | 1 | Hide YouTube logo |
| `iv_load_policy` | 3 | Hide annotations/captions |

### Files to Edit

#### 1. Templates HTML

##### File: `templates/components/products/product-view.html`

**Location 1 - Main video embed (line ~987):**
```html
<!-- BEFORE -->
src="//www.youtube.com/embed/{{product.videos.featured.id}}?rel=0"

<!-- AFTER -->
src="//www.youtube.com/embed/{{product.videos.featured.id}}?rel=0&showinfo=0&modestbranding=1&iv_load_policy=3"
```

**Location 2 - Image gallery videos (line ~196):**
```html
<!-- BEFORE -->
data-video-url="//www.youtube.com/embed/{{id}}?autoplay=1&mute=1&enablejsapi=1"

<!-- AFTER -->
data-video-url="//www.youtube.com/embed/{{id}}?autoplay=1&mute=1&enablejsapi=1&rel=0&showinfo=0&modestbranding=1&iv_load_policy=3"
```

**Location 3 - Navigation thumbnails (line ~315):**
```html
<!-- BEFORE -->
data-video-url="//www.youtube.com/embed/{{id}}?autoplay=1&mute=1&enablejsapi=1"

<!-- AFTER -->
data-video-url="//www.youtube.com/embed/{{id}}?autoplay=1&mute=1&enablejsapi=1&rel=0&showinfo=0&modestbranding=1&iv_load_policy=3"
```

##### File: `templates/components/products/product-view-game.html`

Similar to `product-view.html`, need to update these locations:
- Main video embed (line ~1037)
- Image gallery videos (line ~196)
- Navigation thumbnails (line ~315)

##### File: `templates/components/products/videos.html`

**Location - Featured video (line ~23):**
```html
<!-- BEFORE -->
src="https://www.youtube.com/embed/{{this.featured.id}}?rel=0"

<!-- AFTER -->
src="https://www.youtube.com/embed/{{this.featured.id}}?rel=0&showinfo=0&modestbranding=1&iv_load_policy=3"
```

#### 2. JavaScript Files

##### File: `assets/js/theme/product/video-gallery.js`

**Location - setMainVideo method (line ~23):**
```javascript
// BEFORE
setMainVideo() {
    this.$player.attr('src', `//www.youtube.com/embed/${this.currentVideo.id}`);
}

// AFTER
setMainVideo() {
    this.$player.attr('src', `//www.youtube.com/embed/${this.currentVideo.id}?rel=0&showinfo=0&modestbranding=1&iv_load_policy=3`);
}
```

##### File: `assets/js/papathemes/youtube-carousel.js`

This file already has the correct configuration in `playerVars`:
```javascript
playerVars: {
    controls: 0,
    disablekb: 1,
    enablejsapi: 1,
    fs: 0,
    rel: 0,           // ✓ Already exists
    showinfo: 0,      // ✓ Already exists
    iv_load_policy: 3, // ✓ Already exists
    modestbranding: 1, // ✓ Already exists
    wmode: 'transparent',
    playsinline: 1,
}
```

### Implementation Steps

#### Step 1: Backup files
```bash
# Create backup before editing
cp templates/components/products/product-view.html templates/components/products/product-view.html.backup
cp templates/components/products/product-view-game.html templates/components/products/product-view-game.html.backup
cp templates/components/products/videos.html templates/components/products/videos.html.backup
cp assets/js/theme/product/video-gallery.js assets/js/theme/product/video-gallery.js.backup
```

#### Step 2: Make the changes
Use an editor to find and replace the YouTube URLs according to the guide above.

#### Step 3: Test changes
```bash
# Run development server
stencil start
```

#### Step 4: Deploy
```bash
# Build theme
stencil bundle

# Upload theme to BigCommerce
stencil push
```

### Results Verification

After implementing the changes, YouTube videos will:

- ✅ Not display video title
- ✅ Not display suggested videos
- ✅ Not display YouTube logo
- ✅ Not display annotations

### Troubleshooting

#### Issue: Videos still show title
**Solution:** Check all YouTube URL locations again, ensure all required parameters are added.

#### Issue: Videos won't play
**Solution:** Check URL syntax, ensure there are no extra or missing characters.

#### Issue: Changes don't take effect
**Solution:**
1. Clear browser cache
2. Check if theme has been built and deployed
3. Check browser console for JavaScript errors

### Notes

- The `showinfo=0` parameter may not work with some newer YouTube videos due to policy changes
- Test with multiple different videos to ensure consistency
- Changes only apply to embedded videos, do not affect YouTube website
