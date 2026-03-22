# wordpress-skill

> Built by **[Artur Ferreira](https://github.com/arturseo-geo)** @ **[The GEO Lab](https://thegeolab.net)**
> [X @TheGEO_Lab](https://x.com/TheGEO_Lab) · [LinkedIn](https://linkedin.com/in/arturgeo) · [Reddit](https://www.reddit.com/user/Alternative_Teach_74/)

![Version](https://img.shields.io/badge/version-2.0.0-blue)
![Licence](https://img.shields.io/badge/licence-MIT-green)
![Claude Code](https://img.shields.io/badge/Claude_Code-skill-blueviolet)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen)](https://github.com/arturseo-geo/wordpress-skill/blob/main/CONTRIBUTING.md)

A comprehensive WordPress skill for Claude Code covering the full WordPress ecosystem: REST API, Gutenberg blocks, WooCommerce, SEO plugins, performance tuning, and site management.

## What This Skill Covers

- REST API operations — CRUD for posts, pages, media, taxonomies, users, comments, plugins
- Gutenberg block creation — all core block types, reusable blocks, block patterns, theme.json
- WooCommerce — products (simple, variable, digital), orders, coupons, inventory, reports
- SEO integration — RankMath and Yoast meta fields, schema markup, sitemap management
- Custom post types and fields — ACF, native meta, REST API registration
- Media management — upload, alt text, WebP optimization, responsive images
- User and role management — Application Passwords, capabilities, API users
- Performance — caching (page, object, browser), CDN, database cleanup, Core Web Vitals
- Multisite management — network admin endpoints, WP-CLI multisite commands
- Migration and bulk operations — batch API, content import/export, URL search-replace
- Security — Application Passwords, role-based access, IP allowlisting, XML-RPC hardening
- WP-CLI — command reference for SSH-accessible servers

## Install

### Via Claude Code plugin marketplace
```bash
/plugin marketplace add arturseo-geo/wordpress-skill
/plugin install wordpress@wordpress-skill
```

### Manual install
```bash
git clone https://github.com/arturseo-geo/wordpress-skill.git ~/.claude/skills/wordpress
```

### Via skills CLI
```bash
npx skills add arturseo-geo/wordpress-skill
```

## File Structure

```
wordpress/
├── SKILL.md                          # Main skill — always loaded
├── README.md                         # This file
├── CONTRIBUTING.md                   # Contribution guidelines
├── SECURITY.md                       # Security policy
├── references/
│   ├── rest-api.md                   # Full REST API endpoints, auth methods, pagination
│   ├── gutenberg.md                  # Block types, attributes, patterns, theme.json
│   ├── seo.md                        # RankMath + Yoast meta fields, schema, sitemaps
│   ├── woocommerce.md                # Products, orders, coupons, inventory, reports
│   └── performance.md                # Caching, image optimization, CDN, database cleanup
└── .github/
    ├── ISSUE_TEMPLATE/
    │   ├── bug-report.md             # Bug report template
    │   └── platform-update.md        # WordPress/plugin update template
    └── pull_request_template.md      # PR template
```

## Contributing

WordPress APIs, plugin interfaces, and best practices evolve constantly.
PRs with sourced updates are always welcome.

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.
Use the issue templates for:
- [Bug reports](https://github.com/arturseo-geo/wordpress-skill/issues/new?template=bug-report.md)
- [Platform/plugin updates](https://github.com/arturseo-geo/wordpress-skill/issues/new?template=platform-update.md)

## Related Repos

- [claude-code-skills](https://github.com/arturseo-geo/claude-code-skills) — Full collection of 12 Claude Code skills
- [mcp-wordpress-setup](https://github.com/arturseo-geo/mcp-wordpress-setup) — WordPress MCP server setup guide

---

## Attributions & Licence

Built and maintained by **[Artur Ferreira](https://github.com/arturseo-geo)** @ **[The GEO Lab](https://thegeolab.net)**.

### Best Practice Attribution

This skill was built following the open source Best Practice Approach —
reading community work for inspiration, then writing original content,
and crediting every source.

**Based on:**
- [Agent Skills specification](https://github.com/anthropics/skills) by Anthropic (Apache 2.0)

**Platform data compiled from official documentation:**
- WordPress REST API Handbook, WordPress Block Editor Handbook
- WooCommerce REST API Documentation
- RankMath and Yoast SEO developer documentation
- Cloudflare, Nginx, Redis official documentation
- Google Core Web Vitals and PageSpeed documentation

All skill content is original writing. No files were copied or adapted from any source. MIT licence.

---

Found this useful? Star the repo and connect:
[thegeolab.net](https://thegeolab.net) · [X @TheGEO_Lab](https://x.com/TheGEO_Lab) · [LinkedIn](https://linkedin.com/in/arturgeo) · [Reddit](https://www.reddit.com/user/Alternative_Teach_74/)

## Changelog

### v2.0.0 (March 2026)
**Added:**
- REST API reference — full endpoints, auth methods, pagination, filtering, batch requests
- Gutenberg blocks reference — all core block types with attributes, reusable blocks, patterns, theme.json
- SEO reference — RankMath and Yoast meta fields, schema markup types, sitemap management
- WooCommerce reference — products, orders, coupons, customers, inventory, reports, webhooks
- Performance reference — caching strategies, image optimization, database cleanup, CDN, Core Web Vitals
- Community files — issue templates, PR template, CONTRIBUTING.md, SECURITY.md

**Enhanced SKILL.md:**
- MCP server authentication setup
- Pages CRUD operations
- Custom fields (ACF and native meta) with REST API
- Extended Gutenberg blocks (table, buttons, columns, group, details)
- SEO plugin integration (RankMath and Yoast with social meta)
- User and role management with Application Passwords
- Site health and performance monitoring
- Multisite management
- Batch API endpoint (WP 6.x+)
- Migration workflows
- WP-CLI command reference
- Security best practices
- Extended error handling table

### v1.0.0 (March 2026) — Initial release
Core skill covering REST API basics, Gutenberg block patterns, WooCommerce products, batch operations, SEO metadata, and error handling.

## Licence

MIT — see LICENSE
