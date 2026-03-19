# MCPVault

A lightweight MCP (Model Context Protocol) server for connecting AI assistants to Obsidian vaults. Read, write, search, and manage your notes directly from Claude, ChatGPT, or any MCP-compatible client.

## Features

- 16 MCP methods covering all vault operations
- **Project-level scoping** - isolate notes per project so AI never mixes contexts
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

## Project Scoping

Organize your vault into project folders and scope the server to one project at a time. This prevents AI from mixing notes across projects.

**Vault structure:**
```
my-vault/
  pulse-central/
    api-notes.md
    deployment.md
  coffee-project/
    ideas.md
    architecture.md
  shared/
    templates.md
```

**Scope to a project with `--project`:**

```bash
# Claude Code - scoped to pulse-central only
claude mcp add obsidian npx @gagandeep023/mcpvault --project pulse-central /path/to/vault

# Claude Desktop
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": ["@gagandeep023/mcpvault@latest", "--project", "pulse-central", "/path/to/vault"]
    }
  }
}

# Run directly
npx @gagandeep023/mcpvault@latest --project pulse-central /path/to/vault
```

When `--project` is set:
- All read/write/search operations are restricted to that project folder
- Notes from other projects are invisible
- Use `list_projects` tool to see available projects
- Use `get_current_project` tool to check which project is active
- Omit `--project` for full vault access

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
| `list_projects` | List available project folders in vault |
| `get_current_project` | Show active project and effective path |

## Development

```bash
npm install
npm start /path/to/vault                        # Full vault access
npm start -- --project myproject /path/to/vault  # Scoped to project
npm test                                         # Run tests
npm run build                                    # Build for distribution
```

## Prerequisites

- Node.js v18.0.0 or later
- [Obsidian](https://obsidian.md) (free, available on macOS/Windows/Linux)
- An MCP-compatible AI client (Claude Code, Claude Desktop, ChatGPT Desktop, etc.)

> **Note:** Obsidian is only needed to create and manage your vault. This server works directly with the vault's markdown files on disk, no Obsidian plugins required. Obsidian does not need to be running for the server to work.

## Auto Setup with Claude Agent

Want a Claude agent to set up everything for you? Paste this prompt into Claude Code:

```
Install @gagandeep023/mcpvault globally, then read the guide and follow it
step by step to set up the Obsidian vault and MCP server for my projects:

npm install -g @gagandeep023/mcpvault
cat $(npm root -g)/@gagandeep023/mcpvault/guide.md
```

The guide covers: installing Obsidian, creating the vault, project-scoped folders, registering the MCP server in Claude Code, and verification steps.

## License

MIT
