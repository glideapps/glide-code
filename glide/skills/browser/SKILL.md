---
name: browser
description: |
  Manages allocation of browser instances to agents for concurrent operations.
  Read this skill when spawning multiple agents that need independent browser sessions.
---

# Browser

This plugin provides 6 independent browser instances for concurrent operations. Each browser has its own profile directory.

## Available Browsers

| Browser | MCP Server | Tool Prefix |
|---------|------------|-------------|
| 1 | browser-1 | `mcp__browser-1__browser_*` |
| 2 | browser-2 | `mcp__browser-2__browser_*` |
| 3 | browser-3 | `mcp__browser-3__browser_*` |
| 4 | browser-4 | `mcp__browser-4__browser_*` |
| 5 | browser-5 | `mcp__browser-5__browser_*` |
| 6 | browser-6 | `mcp__browser-6__browser_*` |

## Login

Each browser has its own profile (`.glide/browser-profile-1` through `-6`) to prevent conflicts.

Run `/login` once to:
1. Authenticate via browser-1
2. Close browser-1 (ensures profile is saved)
3. Copy `browser-profile-1` to `browser-profile-2` through `browser-profile-6`

After login, all browsers share the same authenticated session (via copied profiles).

**Re-sync profiles** by running `/login` again if:
- Sessions expire
- You add data to browser-1 that others need
- A browser shows login screen

## Assigning Browsers to Agents

When spawning agents for parallel work, assign each a browser number:

```
"Build the Tasks screen using browser 1"
"Build the Projects screen using browser 2"
"Review the Employees screen design using browser 3"
```

## Agent Tool Usage

Agents must use ONLY their assigned browser's tools:

- Browser 1 agent uses: `mcp__browser-1__browser_navigate`, `mcp__browser-1__browser_snapshot`, etc.
- Browser 2 agent uses: `mcp__browser-2__browser_navigate`, `mcp__browser-2__browser_snapshot`, etc.

If no browser is assigned, default to browser-1.

## Use Cases

**Parallel Screen Building**
- Agent 1 (browser-1): Build Tasks screen
- Agent 2 (browser-2): Build Projects screen
- Agent 3 (browser-3): Build Settings screen

**Build + Review**
- Agent 1 (browser-1): Continue building screens
- Agent 2 (browser-2): Design review completed screens

**Build + Data Editing**
- Agent 1 (browser-1): Build UI in Layout Editor
- Agent 2 (browser-2): Edit tables in Data Editor

## Cleanup

Browsers are managed by Playwright MCP. They launch on first use and close when the Claude session ends. No manual cleanup needed.

## Profile Locations

- `.glide/browser-profile-1` - Primary profile (used for login)
- `.glide/browser-profile-2` through `.glide/browser-profile-6` - Copies of primary
