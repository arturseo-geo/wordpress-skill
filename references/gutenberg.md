# Gutenberg Blocks Reference

## Block Markup Format

Every Gutenberg block follows this structure:
```html
<!-- wp:block-name {"attribute":"value"} -->
<html-content />
<!-- /wp:block-name -->
```

Self-closing blocks (no inner content):
```html
<!-- wp:block-name {"attribute":"value"} /-->
```

---

## Core Block Types

### Text Blocks

**Paragraph**
```html
<!-- wp:paragraph {"align":"center","fontSize":"large","dropCap":true} -->
<p class="has-text-align-center has-large-font-size has-drop-cap">Text</p>
<!-- /wp:paragraph -->
```

**Heading** (levels 1-6)
```html
<!-- wp:heading {"level":2,"textAlign":"center"} -->
<h2 class="wp-block-heading has-text-align-center">Heading Text</h2>
<!-- /wp:heading -->
```

**List**
```html
<!-- wp:list -->
<ul class="wp-block-list">
  <li>Item one</li>
  <li>Item two<!-- wp:list -->
    <ul class="wp-block-list"><li>Nested item</li></ul>
  <!-- /wp:list --></li>
</ul>
<!-- /wp:list -->

<!-- wp:list {"ordered":true,"start":5,"type":"1"} -->
<ol class="wp-block-list" start="5" type="1">
  <li>Fifth item</li>
</ol>
<!-- /wp:list -->
```

**Quote**
```html
<!-- wp:quote {"className":"is-style-large"} -->
<blockquote class="wp-block-quote is-style-large"><p>Quote text here.</p><cite>Attribution</cite></blockquote>
<!-- /wp:quote -->
```

**Pullquote**
```html
<!-- wp:pullquote -->
<figure class="wp-block-pullquote"><blockquote><p>Highlighted quote</p><cite>Source</cite></blockquote></figure>
<!-- /wp:pullquote -->
```

**Code**
```html
<!-- wp:code -->
<pre class="wp-block-code"><code lang="javascript" class="language-javascript">const x = 1;</code></pre>
<!-- /wp:code -->
```

**Preformatted**
```html
<!-- wp:preformatted -->
<pre class="wp-block-preformatted">Preformatted text preserves spacing</pre>
<!-- /wp:preformatted -->
```

**Verse**
```html
<!-- wp:verse -->
<pre class="wp-block-verse">Line one
Line two
Line three</pre>
<!-- /wp:verse -->
```

**Details (Accordion)**
```html
<!-- wp:details -->
<details class="wp-block-details"><summary>Click to expand</summary>
<!-- wp:paragraph -->
<p>Hidden content revealed on click.</p>
<!-- /wp:paragraph -->
</details>
<!-- /wp:details -->
```

---

### Media Blocks

**Image**
```html
<!-- wp:image {"id":123,"sizeSlug":"large","linkDestination":"media","align":"center"} -->
<figure class="wp-block-image aligncenter size-large">
  <a href="full-url.jpg"><img src="url-1024x768.jpg" alt="Description" class="wp-image-123"/></a>
  <figcaption class="wp-element-caption">Caption text</figcaption>
</figure>
<!-- /wp:image -->
```

Size slugs: `thumbnail`, `medium`, `medium_large`, `large`, `full`, `1536x1536`, `2048x2048`

**Gallery**
```html
<!-- wp:gallery {"linkTo":"none","columns":3,"imageCrop":true} -->
<figure class="wp-block-gallery has-nested-images columns-3 is-cropped">
<!-- wp:image {"id":101,"sizeSlug":"large"} -->
<figure class="wp-block-image size-large"><img src="img1.jpg" alt="" class="wp-image-101"/></figure>
<!-- /wp:image -->
<!-- wp:image {"id":102,"sizeSlug":"large"} -->
<figure class="wp-block-image size-large"><img src="img2.jpg" alt="" class="wp-image-102"/></figure>
<!-- /wp:image -->
</figure>
<!-- /wp:gallery -->
```

**Video**
```html
<!-- wp:video {"id":456} -->
<figure class="wp-block-video"><video controls src="video.mp4"></video></figure>
<!-- /wp:video -->
```

**Audio**
```html
<!-- wp:audio {"id":789} -->
<figure class="wp-block-audio"><audio controls src="audio.mp3"></audio></figure>
<!-- /wp:audio -->
```

**Cover**
```html
<!-- wp:cover {"url":"bg.jpg","id":123,"dimRatio":50,"overlayColor":"black","minHeight":400} -->
<div class="wp-block-cover" style="min-height:400px">
  <span class="wp-block-cover__background has-black-background-color has-background-dim-50 has-background-dim"></span>
  <img class="wp-block-cover__image-background wp-image-123" src="bg.jpg" alt=""/>
  <div class="wp-block-cover__inner-container">
    <!-- wp:heading {"textAlign":"center","level":2,"textColor":"white"} -->
    <h2 class="wp-block-heading has-text-align-center has-white-color has-text-color">Hero Title</h2>
    <!-- /wp:heading -->
  </div>
</div>
<!-- /wp:cover -->
```

**Embed** (YouTube, Vimeo, Twitter, etc.)
```html
<!-- wp:embed {"url":"https://www.youtube.com/watch?v=dQw4w9WgXcQ","type":"video","providerNameSlug":"youtube","responsive":true} -->
<figure class="wp-block-embed is-type-video is-provider-youtube wp-block-embed-youtube wp-embed-aspect-16-9 wp-has-aspect-ratio">
  <div class="wp-block-embed__wrapper">https://www.youtube.com/watch?v=dQw4w9WgXcQ</div>
</figure>
<!-- /wp:embed -->
```

---

### Layout Blocks

**Group**
```html
<!-- wp:group {"layout":{"type":"constrained"},"backgroundColor":"light-gray"} -->
<div class="wp-block-group has-light-gray-background-color has-background">
  <!-- inner blocks -->
</div>
<!-- /wp:group -->
```

Layout types: `constrained` (centered, max-width), `flex` (flexbox), `grid`, `default` (flow)

**Columns**
```html
<!-- wp:columns {"isStackedOnMobile":true} -->
<div class="wp-block-columns is-not-stacked-on-mobile">
<!-- wp:column {"width":"66.66%"} -->
<div class="wp-block-column" style="flex-basis:66.66%">
  <!-- wider column content -->
</div>
<!-- /wp:column -->
<!-- wp:column {"width":"33.33%"} -->
<div class="wp-block-column" style="flex-basis:33.33%">
  <!-- narrower column content -->
</div>
<!-- /wp:column -->
</div>
<!-- /wp:columns -->
```

**Row (Flex Group)**
```html
<!-- wp:group {"layout":{"type":"flex","flexWrap":"nowrap","justifyContent":"space-between"}} -->
<div class="wp-block-group">
  <!-- items arranged horizontally -->
</div>
<!-- /wp:group -->
```

**Spacer**
```html
<!-- wp:spacer {"height":"50px"} -->
<div style="height:50px" class="wp-block-spacer"></div>
<!-- /wp:spacer -->
```

**Separator**
```html
<!-- wp:separator {"className":"is-style-wide"} -->
<hr class="wp-block-separator has-alpha-channel-opacity is-style-wide"/>
<!-- /wp:separator -->
```

Styles: `is-style-default`, `is-style-wide`, `is-style-dots`

---

### Widget / Dynamic Blocks

**Table of Contents** (varies by plugin)
```html
<!-- wp:rank-math/toc-block /-->
<!-- wp:yoast/table-of-contents /-->
```

**Shortcode** (legacy compatibility)
```html
<!-- wp:shortcode -->
[contact-form-7 id="1234" title="Contact"]
<!-- /wp:shortcode -->
```

**Custom HTML**
```html
<!-- wp:html -->
<div class="custom-widget">Any raw HTML here</div>
<!-- /wp:html -->
```

**Latest Posts**
```html
<!-- wp:latest-posts {"postsToShow":5,"displayPostContent":true,"excerptLength":20,"displayFeaturedImage":true,"featuredImageSizeSlug":"medium","categories":[{"id":3}]} /-->
```

**Table**
```html
<!-- wp:table {"hasFixedLayout":true,"className":"is-style-stripes"} -->
<figure class="wp-block-table is-style-stripes">
<table class="has-fixed-layout">
  <thead><tr><th>Header 1</th><th>Header 2</th></tr></thead>
  <tbody>
    <tr><td>Cell 1</td><td>Cell 2</td></tr>
    <tr><td>Cell 3</td><td>Cell 4</td></tr>
  </tbody>
  <tfoot><tr><td>Footer 1</td><td>Footer 2</td></tr></tfoot>
</table>
<figcaption class="wp-element-caption">Table caption</figcaption>
</figure>
<!-- /wp:table -->
```

---

### Interactive Blocks

**Buttons**
```html
<!-- wp:buttons {"layout":{"type":"flex","justifyContent":"center"}} -->
<div class="wp-block-buttons">
<!-- wp:button {"backgroundColor":"vivid-cyan-blue","textColor":"white","className":"is-style-fill"} -->
<div class="wp-block-button is-style-fill"><a class="wp-block-button__link has-white-color has-vivid-cyan-blue-background-color has-text-color has-background wp-element-button" href="https://example.com">Primary CTA</a></div>
<!-- /wp:button -->
<!-- wp:button {"className":"is-style-outline"} -->
<div class="wp-block-button is-style-outline"><a class="wp-block-button__link wp-element-button" href="/about">Learn More</a></div>
<!-- /wp:button -->
</div>
<!-- /wp:buttons -->
```

**Search**
```html
<!-- wp:search {"label":"Search","placeholder":"Search the site...","buttonText":"Search","buttonPosition":"button-inside"} /-->
```

---

## Reusable Blocks

Stored as `wp_block` custom post type.

```bash
# List all reusable blocks
GET /wp-json/wp/v2/blocks

# Create reusable block
POST /wp-json/wp/v2/blocks
{
  "title": "Newsletter CTA",
  "content": "<!-- wp:group --><div class=\"wp-block-group\"><!-- wp:heading {\"level\":3} --><h3>Subscribe</h3><!-- /wp:heading --><!-- wp:paragraph --><p>Get weekly updates.</p><!-- /wp:paragraph --></div><!-- /wp:group -->",
  "status": "publish"
}

# Use in content
<!-- wp:block {"ref":789} /-->
```

---

## Block Patterns

Predefined block layouts registered via PHP or the Patterns Directory.

```bash
# List available patterns
GET /wp-json/wp/v2/block-patterns/patterns

# List pattern categories
GET /wp-json/wp/v2/block-patterns/categories
```

Register a custom pattern in a theme or plugin:

```php
register_block_pattern('mytheme/hero-section', [
    'title'       => 'Hero Section',
    'description' => 'Full-width hero with heading and CTA',
    'categories'  => ['featured'],
    'content'     => '<!-- wp:cover {"dimRatio":50} -->...',
]);
```

---

## Global Styles and Theme.json

Block themes use `theme.json` to define design tokens:

```json
{
  "version": 3,
  "settings": {
    "color": {
      "palette": [
        {"slug": "primary", "color": "#1a73e8", "name": "Primary"},
        {"slug": "secondary", "color": "#34a853", "name": "Secondary"}
      ]
    },
    "typography": {
      "fontSizes": [
        {"slug": "small", "size": "14px", "name": "Small"},
        {"slug": "large", "size": "24px", "name": "Large"}
      ]
    },
    "spacing": {
      "units": ["px", "rem", "%"]
    }
  }
}
```

Use theme.json values in blocks:
```html
<!-- wp:paragraph {"textColor":"primary","fontSize":"large"} -->
<p class="has-primary-color has-text-color has-large-font-size">Styled text</p>
<!-- /wp:paragraph -->
```

---

## Common Block Attributes

| Attribute | Type | Used By |
|-----------|------|---------|
| `align` | `left`, `center`, `right`, `wide`, `full` | Most blocks |
| `className` | String | All blocks — custom CSS class |
| `anchor` | String | All blocks — HTML id for linking |
| `backgroundColor` | Slug from palette | Blocks with color support |
| `textColor` | Slug from palette | Blocks with color support |
| `fontSize` | Slug from font sizes | Text blocks |
| `style` | Object | Inline custom styles |
| `lock` | `{"move":true,"remove":true}` | Prevent editing |
