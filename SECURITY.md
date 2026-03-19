# Security Policy

## Reporting a Vulnerability

Report security issues via GitHub Issues at https://github.com/Gagandeep023/mcpvault/issues

## Security Measures

- Path traversal prevention: all paths validated within vault boundaries
- Automatic filtering of `.obsidian/`, `.git/`, `node_modules/`
- Frontmatter validation: blocks functions, symbols, dangerous YAML
- Confirmation required for destructive operations (delete)
- stdio transport only (no network exposure)
