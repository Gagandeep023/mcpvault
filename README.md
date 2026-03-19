# MCPVault

A lightweight MCP (Model Context Protocol) server for connecting AI assistants to Obsidian vaults. Read, write, search, and manage your notes directly from Claude, ChatGPT, or any MCP-compatible client.

## Features

- 14 MCP methods covering all vault operations
- Full-text search with BM25 relevance reranking
- Safe frontmatter parsing and validation
- Read, write, patch, delete, move notes
- Batch operations and tag management
- Token-optimized responses (40-60% smaller)
- Zero Obsidian plugins required, works directly with vault files
- Path traversal protection and security boundaries

## Setup

### 1. Install Obsidian

Download and install [Obsidian](https://obsidian.md) (free):

**macOS:**
```bash
brew install --cask obsidian
```

**Windows:**
Download from [obsidian.md/download](https://obsidian.md/download) and run the installer.

**Linux:**
```bash
# Snap
sudo snap install obsidian --classic

# Or download the AppImage from obsidian.md/download
```

### 2. Create a Vault

Open Obsidian and create a new vault. Note the vault path (e.g. `~/Documents/my-vault`).

### 3. Connect to Your AI Client

**Claude Code:**
```bash
claude mcp add obsidian npx @gagandeep023/mcpvault /path/to/your/vault
```

**Claude Desktop** - add to `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": ["@gagandeep023/mcpvault@latest", "/path/to/your/vault"]
    }
  }
}
```

**Run directly:**
```bash
npx @gagandeep023/mcpvault@latest /path/to/your/vault
```

Replace `/path/to/your/vault` with your actual vault path.

## API Methods

| Method | Description |
|---|---|
| `read_note` | Read a note with parsed frontmatter |
| `write_note` | Write/append/prepend to a note |
| `patch_note` | Find and replace text in a note |
| `delete_note` | Delete a note (with confirmation) |
| `move_note` | Move or rename a note |
| `move_file` | Move any file (binary-safe) |
| `list_directory` | List files and folders |
| `read_multiple_notes` | Batch read up to 10 notes |
| `search_notes` | Full-text search with BM25 ranking |
| `get_frontmatter` | Extract frontmatter only |
| `update_frontmatter` | Update frontmatter without changing content |
| `get_notes_info` | Get metadata for multiple notes |
| `get_vault_stats` | Vault-level statistics |
| `manage_tags` | Add, remove, or list tags |

## Development

```bash
npm install
npm start /path/to/vault    # Run locally
npm test                     # Run tests
npm run build                # Build for distribution
```

## Prerequisites

- Node.js v18.0.0 or later
- [Obsidian](https://obsidian.md) (free, available on macOS/Windows/Linux)
- An MCP-compatible AI client (Claude Code, Claude Desktop, ChatGPT Desktop, etc.)

> **Note:** Obsidian is only needed to create and manage your vault. This server works directly with the vault's markdown files on disk, no Obsidian plugins required. Obsidian does not need to be running for the server to work.

## License

MIT
