---
name: wordpress
description: Publish, manage, and optimize WordPress content via the WordPress REST API. Use this skill whenever the user wants to create or update WordPress posts, pages, or custom post types; manage categories, tags, or taxonomies; upload media or set featured images; work with Gutenberg blocks; configure WooCommerce products; query or bulk-edit content; or automate any WordPress workflow. Trigger for any mention of WordPress, WP, WooCommerce, Gutenberg, wp-json, wp-cli, plugins, themes, or publishing to a WordPress site. Also trigger when the user wants to migrate, audit, or batch-process WordPress content.
---

# WordPress Skill

## Authentication Setup

WordPress REST API requires authentication. Ask the user for:
1. **Site URL** (e.g., `https://example.com`)
2. **Auth method** — prefer Application Passwords (WP 5.6+):
   - Users → Profile → Application Passwords → Add New
   - Returns `username:app-password` — base64 encode for Basic Auth header
3. **API base**: `{site_url}/wp-json/wp/v2/`

```javascript
const headers = {
  'Authorization': 'Basic ' + btoa('username:app-password'),
  'Content-Type': 'application/json'
};
```

### MCP Server Authentication (Recommended)
When a WordPress MCP server is configured, use its tools directly instead of raw HTTP.
MCP tools handle auth automatically — no need to manage headers or tokens.

```jsonc
// .mcp.json — example WordPress MCP config
{
  "mcpServers": {
    "wordpress": {
      "command": "npx",
      "args": ["-y", "@anthropic/wordpress-mcp"],
      "env": {
        "WP_URL": "https://example.com",
        "WP_USERNAME": "admin",
        "WP_APP_PASSWORD": "xxxx xxxx xxxx xxxx"
      }
    }
  }
}
```

### Security Best Practices
- **Never store credentials in code** — use environment variables or `.env` files
- Application Passwords are scoped to the user's role — create a dedicated API user with minimal permissions
- Rotate Application Passwords quarterly
- Use HTTPS exclusively — never send credentials over HTTP
- Add IP allowlisting in `.htaccess` or nginx if the API is only called from known servers
- Disable XML-RPC if not needed: `add_filter('xmlrpc_enabled', '__return_false');`

---

## Core Operations

### Create / Update a Post
```bash
POST /wp-json/wp/v2/posts
{
  "title": "Post Title",
  "content": "<p>HTML or Gutenberg blocks</p>",
  "status": "draft" | "publish" | "private" | "future",
  "date": "2026-03-22T10:00:00",
  "slug": "post-slug",
  "excerpt": "Short summary",
  "categories": [id1, id2],
  "tags": [id1, id2],
  "featured_media": media_id,
  "meta": { "custom_field": "value" },
  "template": "template-name.php"
}

# Update: PATCH /wp-json/wp/v2/posts/{id}
# Delete: DELETE /wp-json/wp/v2/posts/{id}?force=true
```

### Pages
```bash
POST /wp-json/wp/v2/pages
{
  "title": "Page Title",
  "content": "...",
  "status": "publish",
  "parent": 0,
  "template": "page-template.php",
  "menu_order": 1
}
```

### Get Posts (with filtering)
```bash
GET /wp-json/wp/v2/posts?status=publish&per_page=100&page=1
GET /wp-json/wp/v2/posts?search=keyword&categories=5
GET /wp-json/wp/v2/posts?orderby=modified&order=desc
GET /wp-json/wp/v2/posts?after=2026-01-01T00:00:00&before=2026-03-01T00:00:00
GET /wp-json/wp/v2/posts?slug=my-post-slug
GET /wp-json/wp/v2/posts?_embed&_fields=id,title,link,excerpt
```

**Pagination**: Response headers include `X-WP-Total` and `X-WP-TotalPages`.

### Upload Media
```bash
POST /wp-json/wp/v2/media
Content-Disposition: attachment; filename="image.jpg"
Content-Type: image/jpeg
[binary data]

# After upload — update alt text and caption:
PATCH /wp-json/wp/v2/media/{id}
{
  "alt_text": "Descriptive alt text",
  "caption": "Photo credit: Source",
  "title": { "raw": "Image Title" }
}
```

### Manage Taxonomies
```bash
GET  /wp-json/wp/v2/categories
POST /wp-json/wp/v2/categories  { "name": "New Cat", "slug": "new-cat", "parent": 0, "description": "Category description" }
GET  /wp-json/wp/v2/tags
POST /wp-json/wp/v2/tags  { "name": "New Tag", "slug": "new-tag" }

# Custom taxonomies (must have show_in_rest: true)
GET  /wp-json/wp/v2/{taxonomy_slug}
POST /wp-json/wp/v2/{taxonomy_slug}  { "name": "Term Name" }
```

### Custom Post Types
```bash
# CPTs must have show_in_rest: true in registration
GET  /wp-json/wp/v2/{cpt_slug}
POST /wp-json/wp/v2/{cpt_slug}
GET  /wp-json/wp/v2/types            # List all registered post types
GET  /wp-json/wp/v2/types/{slug}     # Get single type details
```

### Custom Fields (ACF and Meta)
```bash
# Native meta fields (must be registered with show_in_rest)
PATCH /wp-json/wp/v2/posts/{id}
{ "meta": { "custom_key": "value" } }

# ACF fields (requires ACF to REST API plugin or ACF PRO 5.11+)
# ACF exposes fields under the "acf" key:
GET /wp-json/wp/v2/posts/{id}?_fields=acf
PATCH /wp-json/wp/v2/posts/{id}
{ "acf": { "field_name": "value", "repeater_field": [{"sub_field": "val"}] } }

# Register meta for REST API visibility:
register_post_meta('post', 'my_field', [
    'show_in_rest' => true,
    'single'       => true,
    'type'         => 'string',
]);
```

---

## Gutenberg Block Patterns

When writing content as Gutenberg blocks (use when site uses block editor):

```html
<!-- wp:paragraph -->
<p>Text here</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":2} -->
<h2 class="wp-block-heading">Heading</h2>
<!-- /wp:heading -->

<!-- wp:image {"id":123,"sizeSlug":"full","linkDestination":"none"} -->
<figure class="wp-block-image size-full"><img src="url" alt="Alt text" class="wp-image-123"/></figure>
<!-- /wp:image -->

<!-- wp:list -->
<ul class="wp-block-list">
  <li>Item one</li>
  <li>Item two</li>
</ul>
<!-- /wp:list -->

<!-- wp:list {"ordered":true} -->
<ol class="wp-block-list">
  <li>First</li>
  <li>Second</li>
</ol>
<!-- /wp:list -->

<!-- wp:quote -->
<blockquote class="wp-block-quote"><p>Quote text</p><cite>Attribution</cite></blockquote>
<!-- /wp:quote -->

<!-- wp:code -->
<pre class="wp-block-code"><code>code here</code></pre>
<!-- /wp:code -->

<!-- wp:table -->
<figure class="wp-block-table"><table><thead><tr><th>Header</th></tr></thead><tbody><tr><td>Cell</td></tr></tbody></table></figure>
<!-- /wp:table -->

<!-- wp:buttons -->
<div class="wp-block-buttons">
<!-- wp:button {"className":"is-style-fill"} -->
<div class="wp-block-button is-style-fill"><a class="wp-block-button__link wp-element-button" href="https://example.com">Click Here</a></div>
<!-- /wp:button -->
</div>
<!-- /wp:buttons -->

<!-- wp:group {"layout":{"type":"constrained"}} -->
<div class="wp-block-group">
<!-- wp:paragraph -->
<p>Grouped content</p>
<!-- /wp:paragraph -->
</div>
<!-- /wp:group -->

<!-- wp:columns -->
<div class="wp-block-columns">
<!-- wp:column -->
<div class="wp-block-column">
<!-- wp:paragraph -->
<p>Column 1</p>
<!-- /wp:paragraph -->
</div>
<!-- /wp:column -->
<!-- wp:column -->
<div class="wp-block-column">
<!-- wp:paragraph -->
<p>Column 2</p>
<!-- /wp:paragraph -->
</div>
<!-- /wp:column -->
</div>
<!-- /wp:columns -->
```

### Reusable Blocks and Block Patterns
```bash
# Reusable blocks are stored as wp_block post type
GET  /wp-json/wp/v2/blocks
POST /wp-json/wp/v2/blocks
{
  "title": "CTA Block",
  "content": "<!-- wp:paragraph --><p>Call to action</p><!-- /wp:paragraph -->",
  "status": "publish"
}

# Use a reusable block in content:
<!-- wp:block {"ref":456} /-->

# Block patterns are registered via PHP — query available patterns:
GET /wp-json/wp/v2/block-patterns/patterns
GET /wp-json/wp/v2/block-patterns/categories
```

---

## WooCommerce Products

```bash
# Requires WooCommerce REST API (separate auth with consumer key/secret)
# Auth: query string ?consumer_key=ck_xxx&consumer_secret=cs_xxx
# Or: Authorization: Basic base64(ck_xxx:cs_xxx)

POST /wp-json/wc/v3/products
{
  "name": "Product Name",
  "type": "simple" | "variable" | "grouped" | "external",
  "regular_price": "29.99",
  "sale_price": "19.99",
  "description": "Full description with HTML",
  "short_description": "Brief summary",
  "sku": "PROD-001",
  "categories": [{"id": 9}],
  "tags": [{"id": 5}],
  "images": [{"src": "url", "alt": "alt text"}],
  "manage_stock": true,
  "stock_quantity": 100,
  "weight": "0.5",
  "dimensions": {"length": "10", "width": "5", "height": "2"},
  "attributes": [{"name": "Color", "options": ["Red", "Blue"], "visible": true, "variation": true}],
  "meta_data": [{"key": "_custom_field", "value": "data"}]
}

# Orders, coupons, customers
GET  /wp-json/wc/v3/orders?status=processing&per_page=50
POST /wp-json/wc/v3/coupons
GET  /wp-json/wc/v3/customers
GET  /wp-json/wc/v3/reports/sales?date_min=2026-01-01&date_max=2026-03-22
```

---

## SEO Plugin Integration

### RankMath (meta fields via REST API)
```json
{
  "meta": {
    "rank_math_title": "SEO Title — %sep% %sitename%",
    "rank_math_description": "Meta description under 160 chars",
    "rank_math_focus_keyword": "primary keyword",
    "rank_math_robots": ["index", "follow"],
    "rank_math_canonical_url": "https://example.com/canonical",
    "rank_math_schema_Article": "{\"@type\":\"Article\"}"
  }
}
```

### Yoast SEO (meta fields via REST API)
```json
{
  "meta": {
    "_yoast_wpseo_title": "SEO Title",
    "_yoast_wpseo_metadesc": "Meta description",
    "_yoast_wpseo_focuskw": "focus keyword",
    "_yoast_wpseo_canonical": "https://example.com/canonical",
    "_yoast_wpseo_opengraph-title": "OG Title",
    "_yoast_wpseo_opengraph-description": "OG Description",
    "_yoast_wpseo_opengraph-image": "https://example.com/og-image.jpg"
  }
}
```

### Schema Markup
Prefer RankMath or Yoast built-in schema. For custom schema, inject via a block:
```html
<!-- wp:html -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Title",
  "author": {"@type": "Person", "name": "Author"},
  "datePublished": "2026-03-22"
}
</script>
<!-- /wp:html -->
```

---

## User and Role Management

```bash
# List users
GET /wp-json/wp/v2/users?per_page=100&roles=editor

# Create user
POST /wp-json/wp/v2/users
{
  "username": "newuser",
  "email": "user@example.com",
  "password": "secure-password",
  "roles": ["editor"],
  "first_name": "First",
  "last_name": "Last"
}

# Update user role
PATCH /wp-json/wp/v2/users/{id}
{ "roles": ["author"] }

# Application Passwords (for API access)
POST /wp-json/wp/v2/users/{id}/application-passwords
{ "name": "CI Pipeline" }
# Returns: { "password": "xxxx xxxx xxxx xxxx" } — store securely, shown only once
```

**Role hierarchy**: Administrator > Editor > Author > Contributor > Subscriber

---

## Site Health and Performance

```bash
# Site health (WP 5.2+)
GET /wp-json/wp-site-health/v1/tests/background-updates
GET /wp-json/wp-site-health/v1/tests/dotorg-communication

# Check WP version
GET /wp-json/  # Root endpoint — returns site name, description, WP version, routes

# Plugin/theme status (requires admin)
GET /wp-json/wp/v2/plugins
PATCH /wp-json/wp/v2/plugins/{plugin_slug}
{ "status": "active" | "inactive" }
```

### Performance Checklist
- Enable object caching (Redis or Memcached)
- Use a page cache plugin (WP Super Cache, W3 Total Cache, LiteSpeed Cache)
- Serve images as WebP with lazy loading (`loading="lazy"`)
- Minify CSS/JS — use Autoptimize or plugin-native minification
- Set up a CDN (Cloudflare, BunnyCDN)
- Schedule database cleanup (post revisions, transients, spam comments)
- Monitor with Query Monitor plugin during development

---

## Multisite Management

```bash
# Multisite endpoints (requires network admin)
GET /wp-json/wp/v2/sites              # List sites in network
POST /wp-json/wp/v2/sites             # Create new site
GET /wp-json/wp/v2/sites/{id}

# Switch site context in WP-CLI:
wp --url=subsite.example.com post list
wp site list --format=table
```

Multisite REST API requires the `wp-multi-network` plugin or custom endpoints on older installs. WP 6.x+ provides native multisite REST support.

---

## Batch Operations

For bulk tasks (update 50+ posts, migrate content), generate a Python or Node.js script:
1. Authenticate once, store token
2. Paginate with `per_page=100&page=N`
3. Process with rate limiting (sleep 0.5s between writes)
4. Log successes and failures to CSV
5. Use `_fields` parameter to reduce payload size

### Batch Endpoint (WP 6.x+)
```bash
POST /wp-json/batch/v1
{
  "requests": [
    {"method": "POST", "path": "/wp/v2/posts", "body": {"title": "Post 1", "status": "draft"}},
    {"method": "POST", "path": "/wp/v2/posts", "body": {"title": "Post 2", "status": "draft"}},
    {"method": "PATCH", "path": "/wp/v2/posts/123", "body": {"status": "publish"}}
  ]
}
```
Limit: 25 requests per batch by default (`rest_get_max_batch_size` filter).

---

## Migration Workflows

### Import from external source
1. Map source fields to WP fields (title, content, slug, date, author, categories)
2. Create categories/tags first, store ID mappings
3. Upload media, store old URL → new URL map
4. Search-replace old URLs in content before creating posts
5. Set `date` and `slug` to preserve original URLs/dates
6. Validate: check for broken internal links, missing images, orphaned media

### Export for migration
```bash
GET /wp-json/wp/v2/posts?per_page=100&page=1&_embed
# _embed includes author, featured image, and taxonomy details inline
# Paginate through all pages, save to JSON
```

---

## Common Workflows

### Publish a new blog post end-to-end:
1. Check/create categories → get IDs
2. Upload featured image → get media ID
3. Write/format content as HTML or Gutenberg blocks
4. POST to /posts with all fields
5. Set SEO meta (RankMath or Yoast fields)
6. Return the published URL

### Audit existing content:
1. GET all posts (paginate)
2. Check: missing excerpts, missing featured images, draft posts older than 90 days, duplicate slugs
3. Check SEO: missing meta descriptions, title length, focus keyword
4. Check media: missing alt text, oversized images
5. Return prioritized fix list

### Scheduled content:
```json
{
  "status": "future",
  "date": "2026-04-01T09:00:00",
  "date_gmt": "2026-04-01T13:00:00"
}
```

---

## Error Handling

| Code | Meaning | Fix |
|---|---|---|
| 401 | Bad credentials | Check app password, re-encode base64 |
| 403 | Insufficient permissions | User needs Editor/Admin role |
| 404 | Post/endpoint not found | Check CPT has `show_in_rest: true` |
| 409 | Conflict (duplicate slug) | Change the slug |
| 422 | Validation error | Check required fields and data types |
| 429 | Rate limited | Back off, increase sleep between requests |
| 500 | Server error | Check PHP error log, memory limit, plugin conflicts |

Always return the post ID and permalink on success.

---

## WP-CLI Reference (SSH Access)

When the user has SSH access, WP-CLI is faster than REST API for many operations:

```bash
wp post create --post_title="Title" --post_status=publish --post_content="Content"
wp post list --post_type=post --post_status=publish --format=csv
wp post update 123 --post_status=draft

wp media import https://example.com/image.jpg --title="Image"
wp media regenerate --yes

wp user create newuser user@example.com --role=editor
wp plugin list --status=active --format=table
wp plugin update --all

wp search-replace 'old-domain.com' 'new-domain.com' --dry-run
wp cache flush
wp rewrite flush
wp cron event run --due-now
wp db optimize
```
