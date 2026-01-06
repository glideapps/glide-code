# Glide Claude Code Plugin

A Claude Code plugin for building Glide applications using browser automation and the Glide API.

## What is This?

This is a **Claude Code plugin** that extends Claude Code with specialized capabilities for building Glide apps:

- **Browser Automation**: Build Glide apps by controlling the browser with Playwright
- **API Integration**: Create tables and import data via the Glide API v2
- **Specialized Agents**: Autonomous agents for building, data import, design review, and QA
- **Design Knowledge**: Best practices for screens, components, and layouts

## Installation

### Option 1: Install from GitHub (Recommended)

```bash
claude plugin add --repo glideapps/glide-claude-code
```

### Option 2: Local Development

Clone and point Claude Code to the local directory:

```bash
git clone git@github.com:glideapps/glide-claude-code.git
claude --plugin-dir /path/to/glide-claude-code/glide
```

## How to Use

Once installed, ask Claude to build a Glide app:

```
"Build a task management app in Glide"
"Create an employee directory from this spreadsheet"
"Help me design a customer portal"
```

Claude will use the appropriate agents to build your app.

## Build Workflow

1. **Clarify use case** - Understand what you want to build
2. **Create app** - Start a blank app in Glide Builder
3. **Analyze file** (if provided) - Extract data structure from spreadsheets
4. **Create tables via API** - Import data using the Glide API
5. **Build screens** - Design the UI with Playwright automation
6. **Design review** - Critique and improve screen layouts
7. **QA verification** - Verify features actually work
8. **Finalize** - Configure access and publish

## Agents

| Agent | Purpose |
|-------|---------|
| **build** | Creates apps and builds screens via browser automation |
| **data** | Creates tables and imports data via Glide API |
| **file-analysis** | Analyzes spreadsheets to extract data models |
| **design-review** | Critiques screens for design quality on mobile/desktop |
| **qa** | Verifies features work before declaring the app ready |
| **app-research** | Explores existing apps to document structure |

## Skills

| Skill | Purpose |
|-------|---------|
| **glide** | Main workflow, agent coordination, builder navigation |
| **design** | Screen structure, component selection, layout patterns |
| **templates** | Browse Template Store for design inspiration |
| **data-modeling** | Tables, columns, relationships |
| **computed-columns** | Math, If-Then-Else, Relations, Rollups |
| **ai** | AI columns (Generate Text, Audio to Text, etc.) |
| **api** | Glide API v2 usage |
| **app-sharing** | Privacy settings and authentication |

## Prerequisites

1. **Glide Account** - Sign in at [go.glideapps.com](https://go.glideapps.com)
2. **Playwright MCP** - Browser automation (configured in plugin)

## First Time Setup

1. Start Claude Code with the plugin installed
2. Ask Claude to build a Glide app
3. A browser window opens - sign in to Glide (first time only)
4. Your session is remembered for future use

## API Token (Optional)

For data operations via the Glide API, get your token from:
**Glide Builder → Data tab → Show API → Copy secret token**

Store it in environment variable `GLIDE_API_TOKEN` or provide it when asked.

## Key Rules

- **Always create Blank apps** - Never use "Import a file"
- **Use API for data import** - Faster and more reliable than UI upload
- **Always include images** - Use placeholder URLs for sample data
- **Design review matters** - Don't skip reviewing detail screens

## License

Copyright © 2024 Glide. All rights reserved.
