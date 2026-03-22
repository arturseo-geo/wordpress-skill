# Contributing to wordpress-skill

Thanks for helping keep this skill accurate and useful. WordPress core, plugin APIs,
and hosting best practices change constantly — community contributions are what keep
this the most up-to-date WordPress skill available.

## What We Welcome

### High-value contributions
- **API endpoint updates** — new REST API routes, changed parameters, deprecated endpoints
- **Plugin integration updates** — RankMath, Yoast, WooCommerce, ACF API changes
- **Gutenberg block changes** — new core blocks, changed attributes, deprecated blocks
- **Performance best practices** — new caching methods, updated server configs, Core Web Vitals changes
- **WooCommerce updates** — new API endpoints, changed order flows, new payment integrations
- **Bug fixes** — incorrect commands, broken workflows, outdated instructions
- **New reference sections** — well-researched additions to any reference file

### What we don't accept
- Unverified data (guesses, approximations without sources)
- Promotional content for specific hosting providers, plugins, or services
- Breaking changes to the SKILL.md frontmatter format without discussion first
- Configuration specific to a single site or brand

---

## How to Contribute

### 1. Check existing issues first
Someone may already be tracking the change you spotted.
> [Open Issues](https://github.com/arturseo-geo/wordpress-skill/issues)

### 2. Fork and create a branch
```bash
git clone https://github.com/YOUR_USERNAME/wordpress-skill.git
cd wordpress-skill
git checkout -b update/woocommerce-v4-endpoints
```

### 3. Make your changes
- Keep edits focused — one topic per PR
- Always include your source (official documentation, not blog posts)
- Update the date reference in the file if the data changed
- Test API endpoints and commands before submitting — do not submit untested code
- Add an entry to the relevant section in `README.md` changelog if it is a significant change

### 4. Submit a Pull Request
Use the PR template — it asks for source links and a brief explanation.
We review all PRs within 7 days.

---

## File Guide

| File | What it contains | How often it changes |
|---|---|---|
| `SKILL.md` | Main skill — workflow overview, quick refs | Rarely |
| `references/rest-api.md` | REST API endpoints, auth, pagination | Occasionally |
| `references/gutenberg.md` | Block types, attributes, patterns | Occasionally |
| `references/seo.md` | RankMath + Yoast meta fields, schema | Frequently |
| `references/woocommerce.md` | Products, orders, coupons, reports | Frequently |
| `references/performance.md` | Caching, CDN, optimization | Occasionally |

**Most common update targets**: `references/woocommerce.md` and `references/seo.md` —
WooCommerce and SEO plugin APIs change with major plugin releases.

---

## Versioning

We follow [Semantic Versioning](https://semver.org/):
- **Patch** (2.0.x): Data corrections, typo fixes, small clarifications
- **Minor** (2.x.0): New reference sections, new block types, new API endpoints
- **Major** (x.0.0): Structural changes to SKILL.md, breaking changes to file layout

---

## Code of Conduct

Be accurate, be useful, cite your sources. That is it.

---

## Questions?

Open a [Discussion](https://github.com/arturseo-geo/wordpress-skill/discussions)
or reach out via [X @TheGEO_Lab](https://x.com/TheGEO_Lab).
