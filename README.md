# reddit-connector-skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-blueviolet)](https://docs.anthropic.com/en/docs/claude-code)
[![No API Key](https://img.shields.io/badge/API_Key-Not_Required-brightgreen)]()

A **Claude Code Skill** that enables AI assistants to browse Reddit — search posts, read comments, view subreddits, and check user profiles. No MCP server, no API key, just `curl` + `jq`.

> **Skills vs MCP**: This is a lightweight alternative to [reddit-connector-mcp](https://github.com/ShellyDeng08/reddit-connector-mcp). Instead of running a persistent MCP server process, this skill teaches Claude to call Reddit's public JSON API directly via CLI — fewer tokens, zero dependencies, same capabilities.

**Keywords**: Reddit API, Reddit Skill, Claude Code Skill, Reddit CLI, Reddit JSON API, Reddit search, Reddit scraper, AI Reddit browser, Claude Code plugin, Reddit connector, Reddit data, subreddit search, Reddit comments API

---

## Features

- **Search Reddit** — search across all of Reddit or within a specific subreddit
- **Browse Subreddits** — get posts sorted by hot, new, top, or rising
- **Read Comments** — fetch full comment threads for any post
- **User Profiles** — view karma, account age, and account details
- **User Activity** — recent posts and comments from any user
- **No API Key Required** — uses Reddit's public `.json` endpoints
- **Zero Dependencies** — only needs `curl` and `jq` (pre-installed on most systems)
- **Cross-Platform** — works on macOS, Linux, and WSL

---

## Quick Start

### Option 1: Claude Code Project Settings (Recommended)

Add to your project's `.claude/settings.json`:

```json
{
  "skills": [
    "/path/to/reddit-connector-skill/reddit-connector-skill.md"
  ]
}
```

### Option 2: User-Level Settings (All Projects)

Add to `~/.claude/settings.json`:

```json
{
  "skills": [
    "/path/to/reddit-connector-skill/reddit-connector-skill.md"
  ]
}
```

### Option 3: Copy into Your Project

```bash
mkdir -p .claude/skills
cp reddit-connector-skill.md .claude/skills/
```

---

## Requirements

| Tool | Install | Notes |
|------|---------|-------|
| `curl` | Pre-installed on macOS/Linux | Ships with all modern OS |
| `jq` | `brew install jq` / `apt install jq` | JSON processor |

---

## Usage

Once installed, just talk to Claude naturally:

> "Search Reddit for posts about Claude AI"
>
> "Show me the top posts this week on r/programming"
>
> "Read the comments on this Reddit post: https://reddit.com/r/..."
>
> "What's u/spez's karma?"
>
> "Show me recent posts from u/spez"

---

## How It Works

Reddit serves JSON at any URL by appending `.json`:

| Endpoint | URL Pattern |
|----------|-------------|
| Search | `reddit.com/search.json?q=QUERY` |
| Subreddit | `reddit.com/r/SUBREDDIT/hot.json` |
| Post comments | Append `.json` to any post URL |
| User profile | `reddit.com/user/USERNAME/about.json` |
| User posts | `reddit.com/user/USERNAME/submitted.json` |

The skill teaches Claude the exact `curl` commands and `jq` filters to query these endpoints. No API key needed — Reddit allows ~60 unauthenticated requests per minute.

---

## Skill vs MCP Comparison

| | reddit-connector-skill | reddit-connector-mcp |
|---|---|---|
| **Setup** | Copy one `.md` file | `npm install` + configure MCP |
| **Runtime** | No process needed | Persistent Node.js server |
| **Token cost** | ~4KB on demand | Tool schemas loaded every session |
| **Dependencies** | `curl` + `jq` | Node.js + npm |
| **API key** | Not required | Not required |
| **Capabilities** | Same 5 operations | Same 5 operations |
| **Platform** | Claude Code only | Any MCP-compatible AI |

Choose **Skill** for lightweight Claude Code usage. Choose **MCP** if you need cross-platform compatibility (Cursor, Windsurf, etc.).

---

## Related Projects

- [reddit-connector-mcp](https://github.com/ShellyDeng08/reddit-connector-mcp) — MCP server version (Node.js)
- [youtube-connector-skill](https://github.com/ShellyDeng08/youtube-connector-skill) — YouTube Skill for Claude Code
- [rednote-analyzer-mcp](https://github.com/ShellyDeng08/rednote-analyzer-mcp) — Xiaohongshu (小红书) MCP analyzer

---

## License

MIT
