# MCP Configuration Manager for HappyCapy

Automates the installation and configuration of multiple MCP (Model Context Protocol) servers in the HappyCapy Claude Code environment (Fly.io sandboxed containers).

**Environment:** HappyCapy/Fly.io Claude Code
**Config Path:** `/home/node/.claude.json` (FIXED)

## What It Does

This skill helps you configure multiple MCP servers globally:

### 🔌 Composio MCP (1000+ Apps)
- 📧 Read and send Gmail emails
- 💬 Post Slack messages
- 📝 Access Notion pages
- 📅 Manage Google Calendar
- 🔗 Connect 1000+ other apps

### 🧠 Memory MCP (Knowledge Graph)
- 💾 Persistent entity storage
- 🔗 Relationship tracking
- 🔍 Knowledge search across sessions

### 🐙 GitHub Copilot MCP (GitHub Operations)
- 🐛 Create and manage issues
- 🔀 Handle pull requests
- 🔍 Search code repositories
- 📝 Read and update files
- 📊 View commits and history

## Usage

Invoke this skill when you need to set up any MCP server:

```
/happycapy-mcp-manager
```

Or use natural language triggers:
- "Install Composio MCP"
- "Configure Gmail access"
- "Set up Memory MCP"
- "Install GitHub Copilot MCP"
- "Configure all MCPs"
- "How do I access Gmail from Claude?"

## What You Need

Before running this skill, prepare based on which MCPs you want:

### For Composio MCP:
1. **Composio API Key** - Get from https://app.composio.dev/settings/api-keys
2. **MCP Server URL** - From Composio dashboard or create new one
   - Format: `https://backend.composio.dev/v3/mcp/{server-id}/mcp?user_id={user-id}`

### For GitHub Copilot MCP:
1. **GitHub Personal Access Token** - Get from https://github.com/settings/tokens
   - Required scopes: `repo`, `read:org`, `read:user`
   - Format: `ghp_xxxxx`

### For Memory MCP:
- No prerequisites needed (uses official `@modelcontextprotocol/server-memory` package)

## Installation Flow

The skill will:

1. ✅ Ask which MCPs you want to install (Composio, Memory, GitHub Copilot)
2. ✅ Collect necessary credentials for each selected MCP
3. ✅ Check your current `/home/node/.claude.json` configuration
4. ✅ Add/update MCP configurations (preserving existing ones)
5. ✅ Clean up any conflicting old configurations
6. ✅ Verify JSON syntax is valid
7. ✅ Guide you to restart Claude Code session
8. ✅ Provide test commands to verify each MCP works

## Configuration Methods

### HTTP Mode (Default & Recommended)

**Pros:**
- No local processes
- Faster and more reliable
- Same pattern as GitHub Copilot MCP

**Config:**
```json
"composio": {
  "type": "http",
  "url": "https://backend.composio.dev/v3/mcp/{server-id}/mcp?user_id={user-id}",
  "headers": {
    "X-API-Key": "ak_xxxxx"
  }
}
```

### stdio Mode (Alternative)

**When to use:** If HTTP mode has issues

**Config:**
```json
"composio": {
  "type": "stdio",
  "command": "npx",
  "args": ["-y", "@composio/mcp"],
  "env": {
    "COMPOSIO_API_KEY": "ak_xxxxx"
  }
}
```

## Authentication Modes

### Auto-Managed (Recommended)

**Setting:** `managed_auth_via_composio: true` in your MCP Server

**Experience:**
- First time using an app → Claude provides OAuth link automatically
- Click link → Authorize → Done
- Future uses work automatically

### Manually-Managed

**Setting:** `managed_auth_via_composio: false` in your MCP Server

**Experience:**
- Pre-authorize apps at https://app.composio.dev/apps
- Then use in Claude Code

## After Installation

Once configured and session restarted, test each MCP:

### Composio MCP
```
读取我最近的 Gmail 邮件
Send email to example@gmail.com with subject "Test"
List my Slack channels
Create Notion page titled "Test"
```

### Memory MCP
```
Create an entity named "Project Alpha" with observation "Uses React and TypeScript"
Search knowledge graph for "React"
Read the complete knowledge graph
```

### GitHub Copilot MCP
```
List my GitHub repositories
Create issue in username/repo with title "Test"
Search for "TODO" in username/repo
Get file contents of README.md in username/repo
```

## Supported Apps

Composio supports 1000+ apps including:

**Communication:**
- Gmail, Outlook, Slack, Discord, Telegram, Microsoft Teams

**Development:**
- GitHub, GitLab, Bitbucket, Jira, Linear, Notion

**Productivity:**
- Google Calendar, Todoist, Asana, Trello, Airtable

**And many more:** https://app.composio.dev/apps

## Troubleshooting

The skill includes troubleshooting for:
- JSON syntax errors in config
- OAuth authorization failures
- Missing toolkits
- API key issues
- MCP not loading after restart

## Technical Details

**Target Environment:** HappyCapy Claude Code (Fly.io sandboxed containers)
**Config File (FIXED):** `/home/node/.claude.json`
**User:** `node`
**Home Directory:** `/home/node`
**Protocol:** Model Context Protocol (MCP)
**Transport Types:**
- HTTP: Remote MCP servers (Composio, GitHub Copilot)
- stdio: Local processes via npx (Memory MCP)

**Cache Locations:**
- npm packages: `/home/node/.npm/_npx/`
- Memory data: `/home/node/.cache/@modelcontextprotocol/server-memory/`
- MCP logs: `/home/node/.cache/claude-cli-nodejs/*/mcp-logs-*/`

## Complete Configuration Example

**File:** `/home/node/.claude.json` (HappyCapy environment)

```json
{
  "firstStartTime": "2026-01-30T10:16:22.623Z",
  "userID": "your-user-id",
  "mcpServers": {
    "composio": {
      "type": "http",
      "url": "https://backend.composio.dev/v3/mcp/660e12ba-ec8d-4916-ad54-e62dd50119ef/mcp?user_id=pg-test-a9d55d9b-4aef-4cc0-a0e1-6f11302464ff",
      "headers": {
        "X-API-Key": "ak_your_api_key"
      }
    },
    "github-copilot": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": {
        "Authorization": "Bearer ghp_your_token"
      }
    },
    "memory": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"],
      "env": {}
    }
  }
}
```

## Supported MCP Servers

This skill currently supports:

| MCP Server | Type | Purpose | Official Link |
|------------|------|---------|---------------|
| **Composio** | HTTP | 1000+ app integrations | https://composio.dev |
| **Memory** | stdio | Knowledge graph storage | https://github.com/modelcontextprotocol/servers/tree/main/src/memory |
| **GitHub Copilot** | HTTP | GitHub operations | https://github.com/features/copilot |

**Extensible:** This skill can be updated to support additional MCPs (Brave Search, Filesystem, Puppeteer, etc.)

## Learn More

- **Composio:** https://composio.dev | https://docs.composio.dev
- **Memory MCP:** https://github.com/modelcontextprotocol/servers/tree/main/src/memory
- **GitHub Copilot:** https://github.com/features/copilot
- **MCP Protocol:** https://modelcontextprotocol.io
- **HappyCapy:** https://happycapy.ai

---

**Created from real-world configuration experience in HappyCapy Claude Code environment (Fly.io)**

**Note:** This skill is specifically designed for HappyCapy/Fly.io sandboxed environments where the config path is fixed at `/home/node/.claude.json`. For local environments, paths may vary.
