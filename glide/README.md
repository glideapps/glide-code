# Glide Plugin

Build complete apps in Glide Builder (go.glideapps.com) using Claude Code with browser automation and API integration.

## Features

- **Browser Automation**: Navigate and interact with the Glide Builder UI using Playwright
- **API Integration**: Efficient data operations using the Glide API v2
- **Comprehensive Knowledge**: Skills covering workflow, data modeling, layout, and more

## Prerequisites

1. **Glide Account**: You must be able to sign in to go.glideapps.com
2. **API Token** (optional): For direct data operations via the Glide API (Big Tables only)

## Installation

### Option 1: From GitHub Repository
```bash
claude --plugin-repo glideapps/glide-code
```

### Option 2: From Local Directory
```bash
claude --plugin-dir /path/to/glide
```

### Option 3: Copy to Project
Copy the `glide` directory to your project's `.claude/plugins/` directory and Claude Code will auto-discover it.

## Browser Setup

This plugin includes a Playwright MCP server that opens a browser window you can watch. The browser uses a **persistent profile** so you only need to sign in to Glide once.

### First Time Setup

1. Start Claude Code with the plugin
2. Ask Claude to build a Glide app - a browser window will open
3. When prompted, sign in to Glide in the browser window
4. After signing in, tell Claude you're ready to continue

Your login will be remembered for future sessions.

## Commands

| Command | Description |
|---------|-------------|
| `/start` | Guided setup - choose new/existing app, store API key, select action |
| `/tip` | Provide expert guidance when stuck - plugin learns and updates its skills |

## Skills

| Skill | Description |
|-------|-------------|
| `glide` | **Read first!** Overall workflow, agent coordination, browser automation |
| `data-modeling` | Tables, columns, Big Tables, data structure |
| `computed-columns` | Math, If-Then-Else, Relations, Rollups, Lookups |
| `layout` | Screens, UI components, forms, actions |
| `ai` | AI columns, summaries, auto-categorization, document scanning |
| `api` | Using Glide API v2 for data operations |
| `app-sharing` | Privacy settings, authentication, publishing, user access |
| `workflows` | Automation, triggers, loops, conditions, API integrations |
| `learnings-log` | Historical record of expert tips integrated into plugin skills |

## Agents

| Agent | Purpose |
|-------|---------|
| `build` | Browser automation - creates apps, adds screens, configures UI |
| `file-analysis` | Analyzes spreadsheets to create app specifications |
| `data` | API operations - creates tables, imports data |
| `design` | Reviews screens and suggests improvements |
| `qa` | Verifies features actually work before declaring app ready |
| `app-research` | Explores existing apps to document structure and features |
| `skill-learner` | Extracts learnings from tips and updates skills (auto-invoked by `/tip`) |

## Quick Start

### Using the /start Command

Run `/start` to get started. This guided workflow will:
1. Guide you through interactive login to Glide in the browser
2. Ask if you want to create a new app or edit an existing one
3. If editing, prompt for the app URL or name
4. Let you choose what to do: build UI, analyze files, review design, etc.
5. Launch the appropriate agent to complete your task

Your authentication is stored locally in `.glide/` and excluded from version control. The API key is requested only when actually creating tables in an app.

### Building Apps Directly
1. Start Claude Code with the plugin
2. Ask Claude to build your app: "Build a task management app in Glide"
3. A browser window opens - sign in to Glide if prompted (first time only)
4. Watch the agent build your app in real-time

### Data Operations
1. Get API token: Data Editor > Show API > Copy secret token (or use `/start` to set it up)
2. Ask Claude: "Import this CSV into my Glide Big Table"

## Key Concepts

### Column-Based Calculations
All calculations in Glide are built by creating computed columns that build on each other, not cell formulas. Chain columns together for complex calculations.

### AI Columns
Glide has powerful AI columns that run inference on your data:
- **Generate Text** - Create summaries, descriptions, recommendations
- **Image to Text** - Extract info from photos (receipts, documents, products)
- **Text to Choice** - Auto-categorize and classify content

### Big Tables vs Native Tables
- **Native Tables**: Per-app, can't be shared
- **Big Tables**: Team-level, can be shared across apps, required for API access

## Tips

- **Data first, then screens** - create tables before building the UI
- **Screen = Table + Collection + Details** - each screen is backed by a table
- **Calculations are columns** - add computed columns, not cell formulas
- **Do not use the Agent tab** - there's an "Agent" tab in Glide Builder, but it's error-prone. Build manually with Data/Layout tabs instead
- Visibility conditions are NOT security - use Row Owners for data protection

## Documentation

- [Glide Documentation](https://www.glideapps.com/docs)
- [Glide API Reference](https://apidocs.glideapps.com/api-reference/v2/general/introduction)
