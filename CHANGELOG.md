# Changelog

## [0.2.0] - 2026-03-20

### Added

- Project-level scoping via `--project <name>` CLI flag
- `list_projects` tool to discover available project folders
- `get_current_project` tool to show active project and effective path
- When scoped, all operations are restricted to the project subfolder

## [0.1.0] - 2026-03-20

### Initial Release

- 14 MCP methods for vault operations
- Full-text search with BM25 relevance reranking
- Safe frontmatter parsing with gray-matter
- Token-optimized responses
- Path traversal protection
- Support for .md, .markdown, .txt, .base, .canvas files
