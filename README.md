# Agent Skills Collection

A collection of open, modular [Agent Skills](https://skills.sh) compatible with the `skills` CLI (`npx skills`) and supported across modern AI coding agents (Claude Code, Cursor, Cline, OpenCode, Codex, Antigravity, and others).

## Installation via `npx skills`

You can install skills directly from this repository using `npx skills`:

### Install all skills from this repository
```bash
npx skills add Tobitap04/skills
```

### Install a specific skill
```bash
# Install GitLab MCP skill
npx skills add Tobitap04/skills --skill gitlab-mcp

# Install Kleinanzeigen Assistant
npx skills add Tobitap04/skills --skill kleinanzeigen-agent

# Install RemNote Flashcard Generator
npx skills add Tobitap04/skills --skill remnote-flashcard-generator
```

### Install globally across projects
Add the `-g` flag to install into your global AI skills directory:
```bash
npx skills add Tobitap04/skills -g
```

---

## Manual Installation

Alternatively, clone or copy the respective skill folder from `skills/` directly into your agent's skills directory:

**Global Directories:**
- `~/.agents/skills/`
- `~/.claude/skills/`
- `~/.copilot/skills/`

**Project-Specific Directories:**
- `.agents/skills/`
- `.claude/skills/`
- `.github/skills/`

---

## Available Skills

### 1. GitLab MCP Skill
- **Path:** `skills/gitlab-mcp/`
- **Identifier:** `gitlab-mcp`
- **Description:** Client-agnostic integration and automation for GitLab instances (GitLab.com and self-hosted) via Model Context Protocol (`@zereight/mcp-gitlab`). Covers multi-client configuration (Claude Desktop, Cursor, Cline, Codex, etc.), token management, and standard operating procedures for issues, merge requests, and repository queries.

### 2. Kleinanzeigen-Assistent
- **Path:** `skills/kleinanzeigen-agent/`
- **Identifier:** `kleinanzeigen-agent`
- **Description:** Automatisierte und schrittweise Erstellung von verkaufsfertigen Kleinanzeigen-Inseraten direkt aus Produktfotos. Beinhaltet visuelle Produktidentifikation mit strukturierter Nutzerbestätigung, deutsche Gebrauchtmarkt-Preisrecherche und verkaufsfertige Anzeigentexte (inkl. 65-Zeichen-Titelbegrenzung und rechtssicherer Privatverkaufsklausel).

### 3. RemNote Flashcard Generator
- **Path:** `skills/remnote-flashcard-generator/`
- **Identifier:** `remnote-flashcard-generator`
- **Description:** Generates optimal flashcards in RemNote format from lecture PDFs and study notes. Applies evidence-based learning principles to create effective study decks. Supports PDF-only input, notes-only refinement, or combined processing with intelligent consolidation of redundant questions.

---

## Repository Structure

```text
skills/
├── skills/
│   ├── gitlab-mcp/
│   │   └── SKILL.md
│   ├── kleinanzeigen-agent/
│   │   └── SKILL.md
│   └── remnote-flashcard-generator/
│       └── SKILL.md
├── README.md
└── LICENSE
```

---

**Standards Compliance:** This repository adheres to the [skills.sh](https://skills.sh) specification.
