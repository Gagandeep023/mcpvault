# MCPVault Setup Guide for Claude Agents

This guide is for a Claude Code agent to set up the MCPVault MCP server on a user's machine. Follow each step in order.

## Prerequisites Check

Run these checks first:

```bash
node --version   # Must be v18.0.0 or later
brew --version   # For installing Obsidian on macOS
```

If Node.js is missing, install it:

```bash
# macOS
brew install node

# Linux
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

## Step 1: Install Obsidian

```bash
# macOS
brew install --cask obsidian

# Linux
sudo snap install obsidian --classic
```

For Windows, download from https://obsidian.md/download

## Step 2: Clone and Build MCPVault

```bash
cd ~/Documents
git clone https://github.com/Gagandeep023/mcpvault.git
cd mcpvault
npm install
npm run build
npm test  # Verify all 156 tests pass
```

## Step 3: Create the Obsidian Vault

```bash
mkdir -p ~/Documents/obsidian-vault/.obsidian
echo "# Welcome" > ~/Documents/obsidian-vault/Welcome.md
```

Then open Obsidian app and point it to `~/Documents/obsidian-vault`.

## Step 4: Create Project Folders

Ask the user what projects they work on, then create folders for each.

Example:

```bash
mkdir -p ~/Documents/obsidian-vault/{work,personal,side-projects}
echo "# Work Notes" > ~/Documents/obsidian-vault/work/README.md
echo "# Personal Notes" > ~/Documents/obsidian-vault/personal/README.md
echo "# Side Projects Notes" > ~/Documents/obsidian-vault/side-projects/README.md
```

## Step 5: Register MCP Server in Claude Code

**Option A: Single vault, no project scoping (simplest)**

```bash
claude mcp add obsidian-vault --scope user -- node ~/Documents/mcpvault/dist/server.js ~/Documents/obsidian-vault
```

**Option B: Project-scoped (recommended for multiple projects)**

For each project directory the user works in, add a project-scoped MCP server.

Use this Python script, replacing the project_map with the user's actual projects:

```python
import json
import os

claude_json_path = os.path.expanduser("~/.claude.json")

with open(claude_json_path, 'r') as f:
    config = json.load(f)

vault = os.path.expanduser("~/Documents/obsidian-vault")
server = os.path.expanduser("~/Documents/mcpvault/dist/server.js")

# CUSTOMIZE THIS: map each project directory to a vault subfolder
# Key = absolute path to the project directory
# Value = subfolder name inside obsidian-vault
project_map = {
    os.path.expanduser("~/Documents/my-work-repo"): "work",
    os.path.expanduser("~/Documents/my-side-project"): "personal",
}

for project_path, vault_folder in project_map.items():
    if project_path in config.get("projects", {}):
        config["projects"][project_path]["mcpServers"] = {
            "obsidian-vault": {
                "command": "node",
                "args": [server, "--project", vault_folder, vault]
            }
        }

with open(claude_json_path, 'w') as f:
    json.dump(config, f, indent=2)

print("MCP servers configured for:", list(project_map.values()))
```

**Option C: User-level fallback + project scoping (best of both)**

Do Option B for specific projects, then add a user-level fallback for everything else:

```bash
claude mcp add obsidian-vault --scope user -- node ~/Documents/mcpvault/dist/server.js ~/Documents/obsidian-vault
```

This way:
- Listed projects get their scoped vault folder
- All other projects get full vault access

## Step 6: Verify Setup

Restart Claude Code, then test:

1. "List all my notes" - should show vault contents
2. "Which project am I scoped to?" - triggers `get_current_project`
3. "Create a test note called hello.md with content Hello World" - tests write
4. "Read hello.md" - tests read
5. "Search for Hello" - tests search
6. "Delete hello.md" - tests delete (will ask for confirmation)

## How It Works

```
Claude Code <--stdio--> MCPVault Server <--filesystem--> Obsidian Vault
```

- The server runs as a child process of Claude Code via stdio
- It reads/writes markdown files directly from the vault folder
- No Obsidian plugins needed, Obsidian does not need to be running
- `--project <name>` restricts all operations to `vault/<name>/` subfolder
- 16 tools available: read, write, search, patch, delete, move, tags, stats, etc.

## Available Tools

| Tool | What it does |
|---|---|
| `read_note` | Read a note with parsed frontmatter |
| `write_note` | Write/append/prepend to a note |
| `patch_note` | Find and replace text in a note |
| `delete_note` | Delete a note (requires confirmation) |
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

## Troubleshooting

- **MCP server not connecting**: Restart Claude Code after config changes
- **"File not found" errors**: Check vault path is correct, run `ls ~/Documents/obsidian-vault`
- **Stale processes**: If you moved the mcpvault folder, kill old processes with `ps aux | grep mcpvault` then `kill <PID>`
- **Permission errors**: Ensure vault directory is readable/writable by your user
- **Build fails**: Run `npm install` then `npm run build` again
