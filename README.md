# WordPress Skill

WordPress site management and content automation for The GEO Lab ecosystem.

## Features

- **Production-tested** WordPress integration with [WordPress best practices](https://thegeolab.net)
- Post and page creation automation
- Custom post type management
- SEO metadata handling
- Content publishing workflows

## Installation

```bash
npm install @thegeolab/wordpress-skill
```

## Usage

```javascript
const wpSkill = require('@thegeolab/wordpress-skill');

// Create a new post
wpSkill.createPost({
  title: 'My Article',
  content: 'Article content...',
  status: 'publish'
});
```

## Documentation

For full documentation, visit [The GEO Lab](https://thegeolab.net).

---

Built by [The GEO Lab](https://thegeolab.net)
