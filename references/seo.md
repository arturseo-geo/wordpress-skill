# WordPress SEO Integration Reference

## RankMath SEO

### Meta Fields (REST API)

Set via the `meta` object when creating or updating posts:

```json
{
  "meta": {
    "rank_math_title": "Custom SEO Title — %sep% %sitename%",
    "rank_math_description": "Meta description, keep under 160 characters for full display in SERPs",
    "rank_math_focus_keyword": "primary keyword",
    "rank_math_robots": ["index", "follow"],
    "rank_math_advanced_robots": {
      "max-snippet": -1,
      "max-video-preview": -1,
      "max-image-preview": "large"
    },
    "rank_math_canonical_url": "https://example.com/canonical-url",
    "rank_math_primary_category": 5,
    "rank_math_pillar_content": "on",
    "rank_math_breadcrumb_title": "Short Breadcrumb Title"
  }
}
```

### RankMath Social Meta
```json
{
  "meta": {
    "rank_math_facebook_title": "Facebook/OG Title",
    "rank_math_facebook_description": "OG Description",
    "rank_math_facebook_image": "https://example.com/og-image.jpg",
    "rank_math_facebook_image_id": 456,
    "rank_math_twitter_use_facebook": "off",
    "rank_math_twitter_title": "Twitter/X Title",
    "rank_math_twitter_description": "Twitter description",
    "rank_math_twitter_image": "https://example.com/twitter-card.jpg",
    "rank_math_twitter_card_type": "summary_large_image"
  }
}
```

### RankMath Schema
```json
{
  "meta": {
    "rank_math_schema_Article": {
      "@type": "Article",
      "name": "%seo_title%",
      "headline": "%seo_title%",
      "description": "%seo_description%",
      "author": {
        "@type": "Person",
        "name": "%name%"
      },
      "datePublished": "%date(Y-m-dTH:i:sP)%",
      "dateModified": "%modified(Y-m-dTH:i:sP)%",
      "image": { "@type": "ImageObject", "url": "%post_thumbnail%" }
    }
  }
}
```

Common schema types: `Article`, `BlogPosting`, `FAQPage`, `HowTo`, `Product`, `LocalBusiness`, `Recipe`, `Review`, `Event`, `Course`, `VideoObject`

### RankMath Variables
Use these in title/description templates:
| Variable | Output |
|----------|--------|
| `%title%` | Post title |
| `%sitename%` | Site name |
| `%sep%` | Separator character |
| `%excerpt%` | Post excerpt |
| `%date%` | Publication date |
| `%modified%` | Last modified date |
| `%category%` | Primary category |
| `%tag%` | First tag |
| `%name%` | Author display name |
| `%search_query%` | Search query (search pages) |
| `%page%` | Page number (paginated) |
| `%seo_title%` | Resolved SEO title |
| `%seo_description%` | Resolved meta description |
| `%post_thumbnail%` | Featured image URL |

---

## Yoast SEO

### Meta Fields (REST API)

```json
{
  "meta": {
    "_yoast_wpseo_title": "Custom SEO Title %%sep%% %%sitename%%",
    "_yoast_wpseo_metadesc": "Meta description under 160 characters",
    "_yoast_wpseo_focuskw": "focus keyword",
    "_yoast_wpseo_canonical": "https://example.com/canonical-url",
    "_yoast_wpseo_meta-robots-noindex": "1",
    "_yoast_wpseo_meta-robots-nofollow": "1",
    "_yoast_wpseo_is_cornerstone": "1",
    "_yoast_wpseo_primary_category": 5
  }
}
```

### Yoast Social Meta
```json
{
  "meta": {
    "_yoast_wpseo_opengraph-title": "OG Title",
    "_yoast_wpseo_opengraph-description": "OG Description",
    "_yoast_wpseo_opengraph-image": "https://example.com/og.jpg",
    "_yoast_wpseo_opengraph-image-id": 456,
    "_yoast_wpseo_twitter-title": "Twitter Title",
    "_yoast_wpseo_twitter-description": "Twitter Description",
    "_yoast_wpseo_twitter-image": "https://example.com/twitter.jpg"
  }
}
```

### Yoast Variables
| Variable | Output |
|----------|--------|
| `%%title%%` | Post title |
| `%%sitename%%` | Site name |
| `%%sep%%` | Separator |
| `%%excerpt%%` | Excerpt |
| `%%date%%` | Date |
| `%%modified%%` | Modified date |
| `%%category%%` | Primary category |
| `%%tag%%` | First tag |
| `%%name%%` | Author |
| `%%searchphrase%%` | Search query |
| `%%page%%` | Page number |
| `%%primary_category%%` | Primary category |

---

## Schema Markup

### Injecting Custom Schema via Gutenberg

```html
<!-- wp:html -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is GEO?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "GEO stands for Generative Engine Optimization..."
      }
    }
  ]
}
</script>
<!-- /wp:html -->
```

### Common Schema Types for WordPress

**Article / BlogPosting**
```json
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "headline": "Post Title",
  "author": {"@type": "Person", "name": "Author Name", "url": "https://example.com/author/name"},
  "publisher": {"@type": "Organization", "name": "Site Name", "logo": {"@type": "ImageObject", "url": "logo.png"}},
  "datePublished": "2026-03-22T10:00:00+00:00",
  "dateModified": "2026-03-22T12:00:00+00:00",
  "image": "https://example.com/featured.jpg",
  "mainEntityOfPage": {"@type": "WebPage", "@id": "https://example.com/post-url"}
}
```

**HowTo**
```json
{
  "@context": "https://schema.org",
  "@type": "HowTo",
  "name": "How to Set Up WordPress",
  "step": [
    {"@type": "HowToStep", "name": "Install WordPress", "text": "Download and install..."},
    {"@type": "HowToStep", "name": "Choose a theme", "text": "Select and activate..."}
  ]
}
```

**Product** (WooCommerce auto-generates this, but can override)
```json
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Product Name",
  "description": "Product description",
  "image": "product.jpg",
  "offers": {
    "@type": "Offer",
    "price": "29.99",
    "priceCurrency": "USD",
    "availability": "https://schema.org/InStock"
  },
  "aggregateRating": {"@type": "AggregateRating", "ratingValue": "4.5", "reviewCount": "120"}
}
```

### Schema Validation
- Test with Google Rich Results Test: `https://search.google.com/test/rich-results`
- Check structured data in Google Search Console → Enhancements
- Use Schema.org validator: `https://validator.schema.org/`

---

## Sitemap Management

### RankMath Sitemap
- Main sitemap: `https://example.com/sitemap_index.xml`
- Post sitemap: `https://example.com/post-sitemap.xml`
- Page sitemap: `https://example.com/page-sitemap.xml`
- Category sitemap: `https://example.com/category-sitemap.xml`
- Settings: RankMath → Sitemap Settings

### Yoast Sitemap
- Main sitemap: `https://example.com/sitemap_index.xml`
- Post sitemap: `https://example.com/post-sitemap.xml`
- Settings: Yoast → Settings → Site Features → XML Sitemaps

### Ping Search Engines After Publishing
Search engines discover new content via sitemaps. After publishing:
1. Google: automatic via Search Console (no manual ping needed since 2023)
2. Bing: submit via Bing Webmaster Tools IndexNow API
3. IndexNow supported by both RankMath and Yoast — enable in plugin settings

---

## SEO Audit Checklist (via REST API)

Run these checks programmatically:

```bash
# 1. Find posts with missing meta descriptions
GET /wp-json/wp/v2/posts?per_page=100&_fields=id,title,slug,meta
# Filter client-side for empty rank_math_description / _yoast_wpseo_metadesc

# 2. Find posts without featured images
GET /wp-json/wp/v2/posts?per_page=100&_fields=id,title,featured_media
# Filter for featured_media == 0

# 3. Find images without alt text
GET /wp-json/wp/v2/media?per_page=100&_fields=id,alt_text,source_url
# Filter for empty alt_text

# 4. Check for duplicate titles
GET /wp-json/wp/v2/posts?per_page=100&_fields=id,title,slug
# Group by title, flag duplicates

# 5. Find thin content (short posts)
GET /wp-json/wp/v2/posts?per_page=100&_fields=id,title,content
# Check content length, flag under 300 words

# 6. Find orphaned pages (no internal links pointing to them)
# Requires crawling or analyzing content for internal links
```

---

## Robots Meta and Indexing

### Per-Post Control
```json
{
  "meta": {
    "rank_math_robots": ["noindex", "nofollow"]
  }
}
```

Or with Yoast:
```json
{
  "meta": {
    "_yoast_wpseo_meta-robots-noindex": "1",
    "_yoast_wpseo_meta-robots-nofollow": "1"
  }
}
```

### robots.txt
Managed via Settings → Reading or via a physical `robots.txt` file.
Default WordPress robots.txt:
```
User-agent: *
Disallow: /wp-admin/
Allow: /wp-admin/admin-ajax.php

Sitemap: https://example.com/sitemap_index.xml
```
