---
name: gitlab-mcp
description: Manage, query, and create GitLab issues, merge requests, commits, and pipelines for any GitLab instance or repository via Model Context Protocol (MCP). Use this skill whenever interacting with GitLab repositories, automating issue management, creating or reviewing merge requests, setting up or troubleshooting GitLab MCP servers, or configuring GitLab Personal Access Tokens across AI clients (Claude Desktop, Cursor, VS Code / Cline, Codex, Antigravity, etc.).
---

# GitLab MCP Skill

This skill provides comprehensive, client-agnostic instructions for integrating and interacting with any **GitLab** instance (GitLab.com or self-hosted) via the Model Context Protocol (MCP).

---

## 1. Prerequisites & MCP Package

- **MCP Server Package:** `@zereight/mcp-gitlab` (Node.js / npx)
- **Supported Platforms:** GitLab.com and self-hosted GitLab CE/EE instances
- **Requirements:** Node.js 18+ installed on your system

---

## 2. Universal MCP Server Configuration

The MCP server connects your AI assistant to a GitLab instance using a Personal Access Token (PAT).

### Standard JSON Configuration Block

Regardless of which tool or AI client is used, the server definition follows this standard MCP schema:

```json
{
  "mcpServers": {
    "gitlab": {
      "command": "npx",
      "args": [
        "-y",
        "@zereight/mcp-gitlab"
      ],
      "env": {
        "GITLAB_PERSONAL_ACCESS_TOKEN": "<YOUR_GITLAB_PAT>",
        "GITLAB_API_URL": "<YOUR_GITLAB_INSTANCE_URL>"
      }
    }
  }
}
```

> **Note on `GITLAB_API_URL`:**
> - For **GitLab.com**: Use `"https://gitlab.com"` (or omit if default).
> - For **Self-hosted GitLab**: Use the base URL of your instance, e.g. `"https://gitlab.example.com"` (without trailing `/api/v4`).

---

### Client-Specific Configuration Locations

Place the JSON configuration block into the appropriate configuration file for your AI environment:

| Client / Environment | Typical Global / Workspace Configuration Path | Key Name in Config |
| :--- | :--- | :--- |
| **Claude Desktop** (macOS) | `~/Library/Application Support/Claude/claude_desktop_config.json` | `mcpServers` |
| **Claude Desktop** (Windows) | `%APPDATA%\Claude\claude_desktop_config.json` | `mcpServers` |
| **Claude Desktop** (Linux) | `~/.config/Claude/claude_desktop_config.json` | `mcpServers` |
| **Cursor** | `Settings` -> `Features` -> `MCP` -> `Add new MCP server` (or `.cursor/mcp.json`) | UI / JSON |
| **VS Code / Cline / Roo-Code** | `~/Library/Application Support/Code/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json` | `mcpServers` |
| **OpenAI Codex / CLI Tools** | `~/.config/mcp/config.json` or custom environment configuration | `mcpServers` |
| **Generic / Custom MCP Hosts** | Respective client configuration file (e.g. `mcp_config.json`) | `mcpServers` |

---

## 3. Token Generation & Verification Guide

When configuring the integration for the first time or if authentication fails:

### Step 3.1: Generate a Personal Access Token (PAT)
1. In your GitLab web interface, navigate to:
   **`User Settings -> Access Tokens`** (or `<GITLAB_URL>/-/user_settings/personal_access_tokens`).
2. Set a **Token name** (e.g., `ai-mcp-assistant`).
3. Select required **Scopes**:
   - `api` (recommended: full read/write access to repository, issues, MRs, pipelines)
   - *or* `read_api` (if strictly read-only access is desired).
4. Click **Create personal access token**.
5. Copy the generated token immediately (starts with `glpat-...`).

### Step 3.2: Insert Token into Configuration
Update your client's MCP configuration with the generated token and your instance URL.

### Step 3.3: Verify Connection via CLI
Test token authentication independently using `curl`:

```bash
curl -s -H "PRIVATE-TOKEN: <YOUR_TOKEN>" "<YOUR_GITLAB_INSTANCE_URL>/api/v4/user"
```

If the response returns your user profile JSON, the token and network connection are fully functional. Restart or reload your AI assistant/client to initialize the MCP tools.

---

## 4. Best Practices for GitLab Issue & MR Creation

When creating issues or merge requests via the MCP server:

### Core Principles
- **Language:** Default to English (or follow project conventions).
- **Conciseness:** Keep descriptions focused, scannable, and actionable. Avoid filler text.
- **Actionability:** Define concrete deliverables using task checkboxes (`- [ ]`).

### Choosing Issue Type
- **`task`**: Use for atomic implementation steps, specific bugfixes, UI tweaks, or single component modifications.
- **`issue`**: Use for broader features, architecture changes, epics, or multi-component initiatives.

### Title Format
Use an imperative, action-oriented format. Capitalize the first letter:
- `[Component/Scope] <Imperative verb> <target/outcome>`
- *or* `<Imperative verb> <target/outcome>`

**Examples:**
- `Add retry logic for API client timeouts`
- `[Frontend] Implement dark mode toggle in navbar`
- `Fix race condition in background worker queue`

### Compact Description Template

```markdown
### Summary
<1-2 sentences outlining the problem, requirement, or objective.>

### Acceptance Criteria
- [ ] <Concrete deliverable 1>
- [ ] <Concrete deliverable 2>
- [ ] <Concrete deliverable 3>

### Technical Notes *(Optional)*
- Affected paths/files
- Relevant endpoints, error codes, or dependencies
```

---

## 5. Common MCP Operations

The `@zereight/mcp-gitlab` server provides standard tools for GitLab automation:

- **Create an Issue:**
  Call `create_issue` with `project_id`, `title`, `description`, and `issue_type` (`issue` or `task`).
- **List / Search Issues:**
  Call `list_issues` with `project_id` and optional filters (`state: "opened"`, `labels`, etc.).
- **View Issue Details:**
  Call `get_issue` with `project_id` and `issue_iid`.
- **Merge Requests:**
  Call `create_merge_request`, `list_merge_requests`, `get_merge_request`, or `mr_discussions`.
- **Repository Inspection:**
  Call `get_file_contents`, `get_repository_tree`, `list_branches`, `list_commits`, or `get_commit_diff`.
