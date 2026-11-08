---
name: gitlab-issue-cli
description: Install, configure, and use the official GitLab CLI (glab) specifically for GitLab issues on GitLab.com, GitLab Self-Managed, or GitLab Dedicated. Use this skill whenever the user asks to create, search, list, read, comment on, update, assign, label, close, or reopen GitLab issues, or needs help installing/authenticating glab for issue workflows. Keep work issue-focused; do not turn this into a general merge request or CI skill.
---

# GitLab Issue CLI Skill (`glab`)

Use GitLab's official `glab` command-line client to manage GitLab issues from a terminal. It supports GitLab.com, GitLab Self-Managed, and GitLab Dedicated. It is a locally installed CLI, not a server or MCP integration. Keep the workflow centered on issues; only mention other GitLab resources when strictly needed to establish project context.

## 1. Install `glab`

Choose the install command for the user's operating system and package manager. The official GitLab CLI README and release page have the current options.

| Platform | Installation |
| --- | --- |
| macOS (Homebrew) | `brew install glab` |
| Linux (Homebrew) | `brew install glab` |
| Windows (WinGet) | `winget install glab.glab` |
| Windows (Chocolatey) | `choco install glab` |
| Windows (Scoop) | `scoop install glab` |
| Linux (Snap) | `sudo snap install glab` |

For other distributions, use the package manager or binary listed in the [official installation options](https://gitlab.com/gitlab-org/cli/-/blob/main/docs/installation_options.md). Avoid guessing a package name for an unsupported package manager.

Confirm installation and discover commands:

```sh
glab --version
glab --help
```

## 2. Authenticate and select an instance

For GitLab.com, start the interactive sign-in and follow its prompts:

```sh
glab auth login
```

In a repository, `glab` can detect the GitLab host from its Git remotes. To specify a self-managed or dedicated instance directly, use its hostname:

```sh
glab auth login --hostname gitlab.example.com
```

Use the web flow when a browser is available. In a headless environment, use `glab auth login --hostname <HOSTNAME> --device` where supported. `glab auth login` also offers token authentication; if a PAT is needed, create one with only the scopes required by the requested operation and follow the CLI prompts. Do not put tokens in command examples, source files, chat, or shell history. For automated use, prefer a securely provided `GITLAB_TOKEN` environment variable over a literal token in a command line.

Check the signed-in account and available hosts:

```sh
glab auth status
```

Credentials are normally stored in the operating system's keyring. In environments without a usable keyring, `glab` may fall back to plaintext configuration storage; note this risk and use the environment's approved secret store where appropriate. Never ask the user to paste a secret into chat.

## 3. How to invoke `glab`

Run `glab` as a normal terminal command through the available terminal/shell tool; it is not an MCP tool call and does not need a server process to be started. In an existing clone, it usually infers the GitLab project from the Git remote, so prefer running commands from the relevant repository. To target a different project, pass `--repo GROUP/PROJECT` (or `-R GROUP/PROJECT`). For a self-managed host, authenticate to that host first; use `GITLAB_HOST` or the documented `--hostname`/repository options when host selection is ambiguous.

When the user asks you to perform GitLab work, first check whether `glab` is installed and authenticated, then run the suitable command and inspect its output. If this environment has no terminal capability, or the CLI is unavailable and cannot be installed in scope, explain the exact commands the user can run instead. Do not claim an operation succeeded unless the command output confirms it.

Before relying on a command or flag, inspect the installed CLI's own help. The CLI evolves; help output is the source of truth for the user's installed version:

```sh
glab <command> --help
```

For example: `glab issue --help`, `glab issue create --help`, or `glab issue update --help`. If an issue-related API operation has no suitable dedicated issue command, check whether `glab api` can handle it and verify its syntax with `glab api --help`. Do not invent command names or flags; if a command is unavailable in this version, report that and offer a supported alternative.

## 4. Issue workflows

### Issues

```sh
# List open issues for the current project (the default)
glab issue list

# Search issues by text
glab issue list --search "timeout"

# List issues assigned to the current user
glab issue list --assignee=@me

# View an issue by its project IID
glab issue view 123

# Create an issue
glab issue create --title "Fix API timeout handling" --description "Summary and acceptance criteria..."

# Comment on an issue
glab issue note 123 --message "I have reproduced this and am investigating."

# Update an issue
glab issue update 123 --label bug --assignee username

# Close or reopen an issue
glab issue close 123
glab issue reopen 123
```

### Issue title and description template

Always write GitLab issue content in English, regardless of the user's language or the project's usual language. This applies to issue titles, descriptions, and comments/notes. Keep communication with the user in their preferred language. Within the issue, use a concise, imperative, action-oriented title. Prefer `[Component/Scope] <Imperative verb> <target/outcome>` when a scope adds clarity, or `<Imperative verb> <target/outcome>` for a compact title:

```text
[Component/Scope] <Imperative verb> <target/outcome>
```

Examples:
- `Add retry logic for API client timeouts`
- `[Frontend] Implement dark mode toggle in navbar`
- `Fix race condition in background worker queue`

Use this description structure when it fits the issue. Keep it focused and actionable; include only relevant sections:

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

Choose issue content based on the request; do not invent requirements, acceptance criteria, labels, assignees, milestones, or technical details. If essential information is missing, ask a focused question. Inspect repository context when available to make the issue accurate.

Before creating an issue, show the proposed title and description and get confirmation unless the user explicitly asked you to create it. The same applies to comments or edits: preview the text and confirm unless explicitly requested. Closing or reopening an issue changes remote state, so only do it when explicitly requested. Never delete issues unless explicitly requested.

When a state-changing action is explicitly requested, execute it using `glab` and verify the command output. Report the issue URL or IID and summarize the result; never claim success without command confirmation.

Use `glab issue list --help`, `glab issue create --help`, `glab issue view --help`, `glab issue note --help`, and `glab issue update --help` to check options supported by the installed version. Issue listing is paginated; when the user asks for all matches, use `--all` or documented pagination options and make clear if results are partial.

## 5. Troubleshooting

- **`glab: command not found`:** Install `glab` for the user's OS, then verify with `glab --version`; check that its install directory is on `PATH`.
- **Not authenticated / 401:** Run `glab auth status`, then authenticate with `glab auth login` for the correct hostname. Check that the token is valid and has the required scope without printing it.
- **Wrong project or host:** Inspect `git remote -v` and run from the intended clone. Pass `--repo GROUP/PROJECT` to select a project explicitly; authenticate to self-managed hosts with `glab auth login --hostname <HOSTNAME>`.
- **Unsupported flag or command:** Check `glab --version` and the relevant `--help`; adapt to the installed version instead of guessing.
- **Network, proxy, or certificate problem:** Don't disable TLS verification as a shortcut. Check the instance URL, network/proxy configuration, and the official [connection documentation](https://docs.gitlab.com/cli/connection/).
- **Need more detail:** Retry a read-only command with `GLAB_DEBUG=true` only when useful; avoid capturing or sharing logs that could contain sensitive information.

## References

- [GitLab CLI documentation](https://docs.gitlab.com/cli/)
- [Issue commands](https://docs.gitlab.com/cli/issue/)
- [Create issues](https://docs.gitlab.com/cli/issue/create/)
- [List issues](https://docs.gitlab.com/cli/issue/list/)
- [Installation options](https://gitlab.com/gitlab-org/cli/-/blob/main/docs/installation_options.md)
- [Authentication](https://docs.gitlab.com/cli/authentication/)
- [Configure the CLI](https://docs.gitlab.com/cli/configuration/)
- [Connect to a GitLab instance](https://docs.gitlab.com/cli/connection/)
