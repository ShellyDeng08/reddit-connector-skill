# reddit-connector-skill

A Claude Code skill that enables browsing Reddit using the public JSON API — no API keys, no MCP server, just `curl` + `jq`.

## What is this?

This is a [Claude Code Skill](https://docs.anthropic.com/en/docs/claude-code) — a markdown file that teaches Claude how to perform tasks. When installed, Claude can search Reddit posts, browse subreddits, read comments, and view user profiles by making HTTP requests to Reddit's public `.json` endpoints.

## Capabilities

- **Search posts** across all of Reddit or within a specific subreddit
- **Browse subreddit posts** sorted by hot, new, top, or rising
- **Read post comments** with author, score, and body text
- **View user profiles** including karma and account age
- **View user activity** — recent posts and comments

## Installation

### Option 1: Project-level (recommended)

Add to your project's `.claude/settings.json`:

```json
{
  "skills": [
    "/path/to/reddit-connector-skill/reddit-connector-skill.md"
  ]
}
```

### Option 2: User-level

Add to `~/.claude/settings.json` to make it available in all projects.

## Requirements

- `curl` (pre-installed on macOS/Linux)
- `jq` (install via `brew install jq` or `apt install jq`)

## How it works

Reddit serves JSON responses at any URL by appending `.json`. For example:

- `https://www.reddit.com/r/programming.json` — subreddit posts
- `https://www.reddit.com/search.json?q=rust` — search results
- `https://www.reddit.com/user/spez/about.json` — user profile

This skill teaches Claude the exact `curl` commands and `jq` filters to query these endpoints and parse the responses into clean, readable output. No API key is needed — Reddit allows ~60 unauthenticated requests per minute.

## Why a Skill instead of an MCP server?

- **Zero dependencies** — no Node.js, no npm install, no running processes
- **No configuration** — no API keys, no environment variables
- **Transparent** — every action is a visible `curl` command
- **Lightweight** — a single markdown file vs. a full TypeScript project

## License

MIT
