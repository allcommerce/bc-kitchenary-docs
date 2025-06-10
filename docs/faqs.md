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
