# WordPress Performance Reference

## Caching Strategies

### Page Caching
Full HTML pages served without PHP execution.

| Plugin | Best For | Notes |
|--------|----------|-------|
| LiteSpeed Cache | LiteSpeed/OpenLiteSpeed servers | Server-level, fastest option |
| WP Super Cache | Simple setups | Generates static HTML files |
| W3 Total Cache | Full control | Complex config, all cache types |
| WP Fastest Cache | Simplicity | Easy UI, good defaults |
| Nginx FastCGI Cache | Nginx servers | Server-level, configure in nginx.conf |

**Nginx FastCGI Cache setup** (common VPS config):
```nginx
# In http block:
fastcgi_cache_path /var/cache/nginx/fastcgi levels=1:2 keys_zone=WORDPRESS:100m inactive=60m max_size=512m;

# In server block:
set $skip_cache 0;
if ($request_method = POST) { set $skip_cache 1; }
if ($query_string != "") { set $skip_cache 1; }
if ($request_uri ~* "/wp-admin/|/wp-json/|/xmlrpc.php|wp-.*.php") { set $skip_cache 1; }
if ($http_cookie ~* "wordpress_logged_in|comment_author") { set $skip_cache 1; }

location ~ \.php$ {
    fastcgi_cache WORDPRESS;
    fastcgi_cache_valid 200 60m;
    fastcgi_cache_bypass $skip_cache;
    fastcgi_no_cache $skip_cache;
    add_header X-FastCGI-Cache $upstream_cache_status;
}
```

Clear cache: `rm -rf /var/cache/nginx/fastcgi/* && systemctl reload nginx`

### Object Caching
Caches database query results in memory.

| Backend | Speed | Setup |
|---------|-------|-------|
| Redis | Fast | `apt install redis-server` + Redis Object Cache plugin |
| Memcached | Fast | `apt install memcached` + Memcached plugin |
| APCu | Moderate | PHP extension, single-server only |

**Redis setup:**
```bash
# Install
apt install redis-server php-redis
systemctl enable redis-server

# wp-config.php
define('WP_REDIS_HOST', '127.0.0.1');
define('WP_REDIS_PORT', 6379);
define('WP_REDIS_DATABASE', 0);
define('WP_REDIS_PREFIX', 'wp_');

# Install plugin and enable
wp plugin install redis-cache --activate
wp redis enable
```

### Browser Caching
Set proper cache headers for static assets:

```nginx
location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot|webp|avif)$ {
    expires 365d;
    add_header Cache-Control "public, immutable";
    add_header Vary "Accept-Encoding";
}
```

### Transient Caching
WordPress transients for expensive operations:
```php
// Store result for 12 hours
$data = get_transient('expensive_query');
if ($data === false) {
    $data = run_expensive_query();
    set_transient('expensive_query', $data, 12 * HOUR_IN_SECONDS);
}
```

Clean expired transients: `wp transient delete --expired`

---

## Image Optimization

### WebP and AVIF Conversion
WordPress 6.1+ supports WebP uploads natively. AVIF support depends on server PHP/GD version.

| Tool | Method | Notes |
|------|--------|-------|
| ShortPixel | Plugin | Bulk optimize, WebP/AVIF, lossy/lossless |
| Imagify | Plugin | By WP Rocket team |
| EWWW Image Optimizer | Plugin | Local optimization option |
| `cwebp` CLI | Server-side | `cwebp -q 80 input.jpg -o output.webp` |

**Serve WebP with Nginx:**
```nginx
location ~* ^(.+)\.(jpe?g|png)$ {
    set $webp "";
    if ($http_accept ~* "webp") { set $webp "A"; }
    if (-f $request_filename.webp) { set $webp "${webp}B"; }
    if ($webp = "AB") { rewrite ^(.+)\.(jpe?g|png)$ $1.$2.webp last; }
}
```

### Lazy Loading
WordPress adds `loading="lazy"` to images by default since WP 5.5. For above-the-fold images:
```html
<!-- wp:image {"id":123,"sizeSlug":"full"} -->
<figure class="wp-block-image size-full">
  <img src="hero.jpg" alt="Hero" loading="eager" fetchpriority="high"/>
</figure>
<!-- /wp:image -->
```

Or via PHP filter:
```php
// Remove lazy loading from first image
add_filter('wp_img_tag_add_loading_attr', function($value, $image, $context) {
    static $count = 0;
    $count++;
    return ($count <= 1) ? false : $value;
}, 10, 3);
```

### Responsive Images
WordPress auto-generates srcset. Ensure themes include proper image sizes:
```php
add_image_size('hero-wide', 1600, 600, true);
add_image_size('card-thumb', 400, 300, true);
```

Regenerate thumbnails after adding sizes: `wp media regenerate --yes`

---

## Database Optimization

### Post Revisions
```php
// wp-config.php — limit revisions
define('WP_POST_REVISIONS', 5);     // Keep only 5 revisions
define('AUTOSAVE_INTERVAL', 120);   // Autosave every 2 minutes (default: 60)
```

**Clean old revisions via WP-CLI:**
```bash
# Count revisions
wp post list --post_type=revision --format=count

# Delete all revisions
wp post delete $(wp post list --post_type=revision --format=ids) --force

# Or use SQL (backup first):
wp db query "DELETE FROM wp_posts WHERE post_type = 'revision'"
```

### Transient Cleanup
```bash
# Delete expired transients
wp transient delete --expired

# Delete all transients (will regenerate as needed)
wp transient delete --all
```

### Spam and Trash Cleanup
```bash
# Delete spam comments
wp comment delete $(wp comment list --status=spam --format=ids) --force

# Delete trashed posts
wp post delete $(wp post list --post_status=trash --format=ids) --force

# Empty trash for all post types
wp post delete $(wp post list --post_type=any --post_status=trash --format=ids) --force
```

### Database Tables Optimization
```bash
# Optimize all tables
wp db optimize

# Repair tables
wp db repair

# Check table sizes
wp db query "SELECT table_name, ROUND(((data_length + index_length) / 1024 / 1024), 2) AS 'Size (MB)' FROM information_schema.tables WHERE table_schema = DATABASE() ORDER BY (data_length + index_length) DESC"
```

### Autoloaded Options Cleanup
Large autoloaded options slow every page load:
```bash
# Find large autoloaded options
wp db query "SELECT option_name, LENGTH(option_value) AS size FROM wp_options WHERE autoload = 'yes' ORDER BY size DESC LIMIT 20"

# Total autoload size (should be under 1MB)
wp db query "SELECT SUM(LENGTH(option_value)) AS total_bytes FROM wp_options WHERE autoload = 'yes'"
```

---

## CDN Integration

### Cloudflare
1. DNS: Point domain to Cloudflare nameservers
2. SSL: Full (Strict) mode
3. Page Rules or Cache Rules for WordPress:
   - Bypass cache for `/wp-admin/*` and `wp-login.php`
   - Cache Everything for static pages
4. APO (Automatic Platform Optimization): WP-specific edge caching for $5/month

### BunnyCDN / KeyCDN / StackPath
Use a CDN plugin or configure in wp-config:
```php
// Rewrite media URLs to CDN
define('WP_CONTENT_URL', 'https://cdn.example.com/wp-content');
```

Or use a plugin: CDN Enabler, WP Rocket CDN, or BunnyCDN plugin.

---

## CSS and JavaScript Optimization

### Minification and Concatenation
| Plugin | Features |
|--------|----------|
| Autoptimize | Minify, combine, inline critical CSS |
| WP Rocket | All-in-one (paid) |
| Asset CleanUp | Remove unused CSS/JS per page |
| Perfmatters | Disable unused scripts per page |

### Critical CSS
Inline above-the-fold CSS and defer the rest:
```php
// Generate critical CSS with tools like criticalcss.com or PurgeCSS
add_action('wp_head', function() {
    echo '<style>' . file_get_contents(get_template_directory() . '/critical.css') . '</style>';
}, 1);
```

### Script Loading
```php
// Defer non-critical scripts
add_filter('script_loader_tag', function($tag, $handle) {
    $defer_scripts = ['comment-reply', 'analytics', 'social-share'];
    if (in_array($handle, $defer_scripts)) {
        return str_replace(' src', ' defer src', $tag);
    }
    return $tag;
}, 10, 2);
```

### Remove Unused WordPress Scripts
```php
// Remove block library CSS on non-block pages
add_action('wp_enqueue_scripts', function() {
    if (!is_singular()) {
        wp_dequeue_style('wp-block-library');
        wp_dequeue_style('wp-block-library-theme');
        wp_dequeue_style('global-styles');
    }
});

// Remove jQuery migrate (if not needed)
add_action('wp_default_scripts', function($scripts) {
    if (!is_admin() && isset($scripts->registered['jquery'])) {
        $scripts->registered['jquery']->deps = array_diff(
            $scripts->registered['jquery']->deps, ['jquery-migrate']
        );
    }
});
```

---

## Server-Level Optimization

### PHP Configuration
```ini
; php.ini or pool.d/www.conf
memory_limit = 256M
max_execution_time = 30
upload_max_filesize = 64M
post_max_size = 64M
opcache.enable = 1
opcache.memory_consumption = 128
opcache.max_accelerated_files = 10000
opcache.revalidate_freq = 60
```

### wp-config.php Performance Settings
```php
// Increase memory limit
define('WP_MEMORY_LIMIT', '256M');
define('WP_MAX_MEMORY_LIMIT', '512M');  // Admin area

// Disable WP Cron (use system cron instead)
define('DISABLE_WP_CRON', true);
// Then add to system crontab:
// */5 * * * * cd /var/www/html && php wp-cron.php > /dev/null 2>&1

// Optimize database connections
define('WP_ALLOW_REPAIR', false);  // Enable only when needed
```

### Gzip / Brotli Compression
```nginx
# Nginx gzip
gzip on;
gzip_vary on;
gzip_comp_level 6;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml text/javascript image/svg+xml;

# Brotli (requires ngx_brotli module)
brotli on;
brotli_comp_level 6;
brotli_types text/plain text/css application/json application/javascript text/xml application/xml text/javascript image/svg+xml;
```

---

## Performance Monitoring

### Query Monitor Plugin
Install during development to identify:
- Slow database queries (> 0.05s)
- Duplicate queries
- HTTP API calls and their response times
- Hooks consuming the most time
- Memory usage per component

### Core Web Vitals
| Metric | Target | What It Measures |
|--------|--------|-----------------|
| LCP (Largest Contentful Paint) | < 2.5s | Loading speed |
| INP (Interaction to Next Paint) | < 200ms | Responsiveness |
| CLS (Cumulative Layout Shift) | < 0.1 | Visual stability |

Monitor via:
- Google Search Console → Core Web Vitals
- PageSpeed Insights: `https://pagespeed.web.dev/`
- Chrome DevTools → Lighthouse
- WebPageTest: `https://www.webpagetest.org/`

### WP-CLI Performance Check
```bash
# Check TTFB
wp eval 'echo "Memory: " . size_format(memory_get_peak_usage()) . "\n";'

# Profile a page load
wp profile stage --url=https://example.com/

# List slow hooks
wp profile hook --all --spotlight --url=https://example.com/

# Check total DB queries
wp eval 'global $wpdb; $wpdb->show_errors(); echo $wpdb->num_queries . " queries\n";'
```

---

## Hosting Considerations

| Type | TTFB | Monthly | Best For |
|------|------|---------|----------|
| Shared | 500ms+ | $5-15 | Low traffic blogs |
| Managed WP (Cloudways, Kinsta, WP Engine) | 100-300ms | $25-100 | Production sites |
| VPS (DigitalOcean, Vultr, Hetzner) | 100-200ms | $5-50 | Full control |
| Dedicated | <100ms | $100+ | High traffic |

**Essential VPS stack for WordPress:**
- Ubuntu 22.04+ / Debian 12+
- Nginx (not Apache) or OpenLiteSpeed
- PHP 8.2+ with OPcache
- MariaDB 10.11+ or MySQL 8.0+
- Redis for object caching
- Let's Encrypt for SSL
