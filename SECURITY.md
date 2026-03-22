# Security Policy

## Scope

This is a Claude Code skill — a collection of markdown instruction files.
It contains no executable code, no authentication tokens, and no user data.

Security concerns for this repo are limited to:
- Incorrect commands that could cause harm if run on a WordPress server (e.g., destructive WP-CLI commands, unsafe nginx configs)
- Instructions that could weaken WordPress security (e.g., incorrect file permissions, exposed API endpoints)
- Misleading authentication guidance that could lead to credential exposure

## Reporting an Issue

If you find content in this skill that could cause harm — a wrong command, a misleading
instruction, an insecure configuration, or anything that could damage someone's WordPress
site or server — please report it privately before opening a public issue.

**Contact**: Open a private security advisory via
[GitHub Security Advisories](https://github.com/arturseo-geo/wordpress-skill/security/advisories/new)
or email **artur@thegeolab.net**, or reach out via [X @TheGEO_Lab](https://x.com/TheGEO_Lab).

We aim to address valid reports within 48 hours.
