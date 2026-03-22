# WordPress REST API Reference

## Base URL

```
https://example.com/wp-json/wp/v2/
```

Check API availability: `GET /wp-json/` — returns site info, namespaces, and all routes.

---

## Authentication Methods

### 1. Application Passwords (Recommended)
Available since WP 5.6. No plugin required.

```bash
# Generate: Users → Profile → Application Passwords → Add New
# Use: Base64 encode "username:app-password"
Authorization: Basic dXNlcm5hbWU6YXBwLXBhc3N3b3Jk
```

**Manage programmatically:**
```bash
# Create
POST /wp-json/wp/v2/users/{user_id}/application-passwords
{ "name": "My App" }
# Response includes the password — store it, shown only once

# List
GET /wp-json/wp/v2/users/{user_id}/application-passwords

# Revoke
DELETE /wp-json/wp/v2/users/{user_id}/application-passwords/{uuid}

# Revoke all
DELETE /wp-json/wp/v2/users/{user_id}/application-passwords
```

### 2. Cookie Authentication (Browser Sessions)
Used automatically when making requests from the WP admin JS context.
Requires a nonce: `_wpnonce` parameter or `X-WP-Nonce` header.

```javascript
// In WP admin JS context:
fetch('/wp-json/wp/v2/posts', {
  headers: { 'X-WP-Nonce': wpApiSettings.nonce }
});
```

### 3. JWT Authentication (via Plugin)
Requires a JWT plugin (e.g., `wp-graphql-jwt-authentication` or `jwt-auth`).

```bash
# Get token
POST /wp-json/jwt-auth/v1/token
{ "username": "admin", "password": "password" }

# Use token
Authorization: Bearer eyJ0eXAiOiJKV1Q...
```

### 4. OAuth 2.0
For third-party integrations. Requires the OAuth Server plugin.

---

## Endpoints Reference

### Posts
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/wp/v2/posts` | List posts |
| POST | `/wp/v2/posts` | Create post |
| GET | `/wp/v2/posts/{id}` | Get single post |
| PATCH | `/wp/v2/posts/{id}` | Update post |
| DELETE | `/wp/v2/posts/{id}` | Trash post |
| DELETE | `/wp/v2/posts/{id}?force=true` | Permanently delete |
| GET | `/wp/v2/posts/{id}/revisions` | List revisions |
| GET | `/wp/v2/posts/{id}/autosaves` | List autosaves |

### Pages
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/wp/v2/pages` | List pages |
| POST | `/wp/v2/pages` | Create page |
| GET | `/wp/v2/pages/{id}` | Get single page |
| PATCH | `/wp/v2/pages/{id}` | Update page |
| DELETE | `/wp/v2/pages/{id}?force=true` | Delete page |

### Media
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/wp/v2/media` | List media |
| POST | `/wp/v2/media` | Upload file |
| GET | `/wp/v2/media/{id}` | Get media item |
| PATCH | `/wp/v2/media/{id}` | Update (alt, caption, title) |
| DELETE | `/wp/v2/media/{id}?force=true` | Delete permanently |

### Taxonomies
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/wp/v2/categories` | List categories |
| POST | `/wp/v2/categories` | Create category |
| PATCH | `/wp/v2/categories/{id}` | Update category |
| DELETE | `/wp/v2/categories/{id}?force=true` | Delete |
| GET | `/wp/v2/tags` | List tags |
| POST | `/wp/v2/tags` | Create tag |
| GET | `/wp/v2/taxonomies` | List all taxonomy types |

### Users
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/wp/v2/users` | List users |
| POST | `/wp/v2/users` | Create user |
| GET | `/wp/v2/users/me` | Current user |
| PATCH | `/wp/v2/users/{id}` | Update user |
| DELETE | `/wp/v2/users/{id}?reassign={id}` | Delete user |

### Comments
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/wp/v2/comments` | List comments |
| POST | `/wp/v2/comments` | Create comment |
| PATCH | `/wp/v2/comments/{id}` | Update/approve |
| DELETE | `/wp/v2/comments/{id}?force=true` | Delete |

### Settings (Admin only)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/wp/v2/settings` | Read all settings |
| PATCH | `/wp/v2/settings` | Update settings |

### Plugins (Admin only, WP 5.5+)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/wp/v2/plugins` | List installed plugins |
| POST | `/wp/v2/plugins` | Install plugin |
| PATCH | `/wp/v2/plugins/{slug}` | Activate/deactivate |
| DELETE | `/wp/v2/plugins/{slug}` | Uninstall |

### Block Types and Patterns
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/wp/v2/block-types` | List registered block types |
| GET | `/wp/v2/block-types/{namespace}/{name}` | Get block type details |
| GET | `/wp/v2/block-patterns/patterns` | List block patterns |
| GET | `/wp/v2/block-patterns/categories` | List pattern categories |
| GET | `/wp/v2/blocks` | List reusable blocks |
| POST | `/wp/v2/blocks` | Create reusable block |

### Search
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/wp/v2/search?search=term` | Search across content types |
| GET | `/wp/v2/search?search=term&type=post` | Search posts only |
| GET | `/wp/v2/search?search=term&subtype=page` | Search pages only |

---

## Query Parameters

### Filtering
| Parameter | Example | Description |
|-----------|---------|-------------|
| `search` | `?search=keyword` | Full-text search |
| `status` | `?status=publish` | Post status |
| `categories` | `?categories=5,12` | Filter by category IDs |
| `categories_exclude` | `?categories_exclude=3` | Exclude categories |
| `tags` | `?tags=8` | Filter by tag IDs |
| `author` | `?author=1` | Filter by author ID |
| `slug` | `?slug=my-post` | Get by slug |
| `before` | `?before=2026-04-01T00:00:00` | Posts before date |
| `after` | `?after=2026-01-01T00:00:00` | Posts after date |
| `modified_before` | `?modified_before=...` | Modified before date |
| `modified_after` | `?modified_after=...` | Modified after date |
| `sticky` | `?sticky=true` | Sticky posts only |

### Sorting
| Parameter | Values |
|-----------|--------|
| `orderby` | `date`, `modified`, `title`, `slug`, `id`, `relevance`, `include`, `menu_order` |
| `order` | `asc`, `desc` |

### Pagination
| Parameter | Default | Max | Description |
|-----------|---------|-----|-------------|
| `per_page` | 10 | 100 | Items per page |
| `page` | 1 | — | Page number |
| `offset` | 0 | — | Skip N items |

**Response headers:**
- `X-WP-Total` — total items matching query
- `X-WP-TotalPages` — total pages

### Field Selection
```bash
# Only return specific fields (reduces payload significantly)
GET /wp-json/wp/v2/posts?_fields=id,title,link,slug,status

# Embed related resources inline (author, featured image, terms)
GET /wp-json/wp/v2/posts?_embed
GET /wp-json/wp/v2/posts?_embed=author,wp:featuredmedia
```

---

## Batch Requests (WP 6.x+)

Process multiple API calls in a single HTTP request:

```bash
POST /wp-json/batch/v1
{
  "validation": "require-all-validate",
  "requests": [
    {"method": "POST", "path": "/wp/v2/posts", "body": {"title": "Post 1", "status": "draft"}},
    {"method": "POST", "path": "/wp/v2/posts", "body": {"title": "Post 2", "status": "draft"}},
    {"method": "PATCH", "path": "/wp/v2/posts/123", "body": {"meta": {"key": "value"}}}
  ]
}
```

- Default limit: 25 requests per batch
- `validation`: `normal` (default) or `require-all-validate` (atomic — all or none)

---

## Error Responses

All errors follow a consistent format:

```json
{
  "code": "rest_forbidden",
  "message": "Sorry, you are not allowed to do that.",
  "data": { "status": 403 }
}
```

Common error codes:
| HTTP | WP Code | Meaning |
|------|---------|---------|
| 400 | `rest_invalid_param` | Bad parameter value |
| 401 | `rest_not_logged_in` | Missing or invalid auth |
| 403 | `rest_forbidden` | Insufficient capabilities |
| 404 | `rest_post_invalid_id` | Resource not found |
| 409 | `rest_duplicate` | Duplicate slug or unique constraint |
| 500 | `rest_error` | Server-side error — check PHP logs |

---

## Custom Endpoints

Register custom routes in a plugin or theme:

```php
add_action('rest_api_init', function() {
    register_rest_route('myplugin/v1', '/data', [
        'methods'  => 'GET',
        'callback' => 'my_callback',
        'permission_callback' => function() {
            return current_user_can('edit_posts');
        },
    ]);
});
```

Discover all available routes: `GET /wp-json/` — the `routes` object lists every registered endpoint.
