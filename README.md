# wordpress-skill

> Built by **[Artur Ferreira](https://github.com/arturseo-geo)** @ **[The GEO Lab](https://thegeolab.net)** · [𝕏 @TheGEO_Lab](https://x.com/TheGEO_Lab) · [LinkedIn](https://linkedin.com/in/arturgeo) · [Reddit](https://www.reddit.com/user/Alternative_Teach_74/)

![Version](https://img.shields.io/badge/version-2.0.0-blue)
![Licence](https://img.shields.io/badge/licence-MIT-green)
![Topics](https://img.shields.io/badge/topics-12-orange)
![Claude Code](https://img.shields.io/badge/Claude_Code-skill-blueviolet)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen)](https://github.com/arturseo-geo/wordpress-skill/blob/main/CONTRIBUTING.md)

The most comprehensive WordPress skill for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — covering the full WordPress ecosystem: REST API, Gutenberg blocks, WooCommerce, RankMath/Yoast SEO, ACF custom fields, media management, multisite, migration, performance tuning, security, and WP-CLI.

## Who This Is For

- **WordPress developers** who want Claude to handle REST API operations, block creation, and WP-CLI commands
- **Content teams** managing posts, pages, media, and taxonomies via Claude Code
- **Agency developers** building WooCommerce stores, custom post types, or multisite networks
- **Anyone with a WordPress site** who wants AI-powered site management

## What Makes This Different

Most WordPress AI tools handle basic post creation. This skill covers the full ecosystem:

- ✅ **REST API operations** — CRUD for posts, pages, media, taxonomies, users, comments, plugins
- ✅ **Gutenberg blocks** — all core block types, reusable blocks, block patterns, theme.json configuration
- ✅ **WooCommerce** — products (simple, variable, digital), orders, coupons, inventory, reports
- ✅ **SEO integration** — RankMath and Yoast meta fields, schema markup, sitemap management
- ✅ **Custom post types & fields** — ACF, native meta, REST API registration
- ✅ **Media management** — upload, alt text, WebP optimisation, responsive images
- ✅ **Performance** — page/object/browser caching, CDN, database cleanup, Core Web Vitals
- ✅ **Multisite** — network admin endpoints, WP-CLI multisite commands
- ✅ **Migration & bulk ops** — batch API, content import/export, URL search-replace
- ✅ **Security** — Application Passwords, role-based access, IP allowlisting, XML-RPC hardening
- ✅ **WP-CLI** — full command reference for SSH-accessible servers
- ✅ **MCP integration** — works with [mcp-wordpress](https://github.com/arturseo-geo/mcp-wordpress-setup) for direct API access

## Install

```bash
# Via Claude Code plugin marketplace
/plugin marketplace add arturseo-geo/wordpress-skill
/plugin install wordpress@wordpress-skill

# Manual install
git clone https://github.com/arturseo-geo/wordpress-skill.git ~/.claude/skills/wordpress

# Via skills CLI
npx skills add arturseo-geo/wordpress-skill

# Or install all 12 skills at once
git clone https://github.com/arturseo-geo/claude-code-skills.git
cp -r claude-code-skills/skills/wordpress ~/.claude/skills/
```

## File Structure

```
wordpress-skill/
├── SKILL.md                          — Main skill — always loaded
├── references/
│   ├── rest-api.md                   — Full REST API endpoints, auth methods, pagination
│   ├── gutenberg.md                  — Block types, attributes, patterns, theme.json
│   ├── seo.md                        — RankMath + Yoast meta fields, schema, sitemaps
│   ├── woocommerce.md                — Products, orders, coupons, inventory, reports
│   └── performance.md                — Caching, image optimisation, CDN, database cleanup
└── .github/                          — Issue templates and PR template
```

## Related Repos

- [claude-code-skills](https://github.com/arturseo-geo/claude-code-skills) — Full collection of 12 skills
- [mcp-wordpress-setup](https://github.com/arturseo-geo/mcp-wordpress-setup) — WordPress MCP server setup guide

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines. PRs welcome.

---

Built and maintained by **[Artur Ferreira](https://github.com/arturseo-geo)** @ **[The GEO Lab](https://thegeolab.net)** · [MIT License](LICENSE)
