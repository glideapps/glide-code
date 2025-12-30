# Glide Plugin

Build complete apps in Glide Builder (go.glideapps.com) using Claude Code with browser automation and API integration.

## Features

- **Browser Automation**: Navigate and interact with the Glide Builder UI using Playwright
- **API Integration**: Efficient data operations using the Glide API v2 and @glideapps/tables npm package
- **Comprehensive Knowledge**: Skills covering navigation, data modeling, components, access management, and best practices

## Prerequisites

1. **Glide Account**: You must be able to sign in to go.glideapps.com
2. **API Token** (optional): For direct data operations via the Glide API (Big Tables only)

## Installation

### Option 1: From GitHub Repository
```bash
claude --plugin-repo glideapps/glide-claude-code
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

1. Start Claude Code with the plugin:
   ```bash
   claude --plugin-dir /path/to/glide
   ```

2. Ask Claude to build a Glide app - a browser window will open

3. When prompted, sign in to Glide in the browser window

4. After signing in, tell Claude you're ready to continue

Your login will be remembered for future sessions (stored in `~/Library/Caches/ms-playwright/`).

## Skills

| Skill | Description |
|-------|-------------|
| `build-workflow` | Overall workflow, agent coordination, key patterns |
| `builder-navigation` | Builder UI navigation, shortcuts, and patterns |
| `data-modeling` | Tables, columns, shared tables, data structure |
| `computed-columns` | Math, If-Then-Else, Relations, Rollups, Lookups |
| `ai` | AI columns, summaries, auto-categorization, document scanning |
| `components` | Screens, UI components, layouts, forms, actions |
| `api` | Using Glide API v2 and @glideapps/tables for data operations |
| `access-management` | Privacy settings, authentication, publishing, user access |

## Agents

### file-analysis
Analyzes spreadsheets and data files before building an app:
1. Examines file structure (sheets, columns, data types)
2. Understands the use case and who will use the app
3. Analyzes formulas and calculations (maps to Glide computed columns)
4. Identifies relationships between tables
5. Creates a specification for the builder agent

### builder
Autonomously builds Glide apps using browser automation:
1. Understanding requirements from conversation
2. Creating tables and data structure
3. Building screens linked to tables
4. Adding computed columns for calculations
5. Configuring components and data bindings
6. Setting up access and inviting users

### data
Manages Glide data programmatically:
1. Creating and managing Big Tables via API
2. Bulk data imports and exports
3. Syncing data from external sources
4. Batch updates and migrations

### design
Reviews and improves screens after they're built:
1. Takes screenshots and analyzes current state
2. Evaluates layout, components, and data density
3. Researches Glide docs for better options
4. Suggests specific improvements for the builder to implement

## Quick Start

### Building Apps (Browser)
1. Start Claude Code with the plugin
2. Ask Claude to build your app: "Build a task management app in Glide"
3. A browser window opens - sign in to Glide if prompted (first time only)
4. Watch the agent build your app in real-time!

### Data Operations (API)
1. Get API token: Data Editor > Show API > Copy secret token
2. Ask Claude: "Import this CSV into my Glide Big Table"

## Configuration

Create `.claude/glide.local.md` for API token storage:

```markdown
# Glide Settings

## API Token
GLIDE_API_TOKEN=your-token-here
```

## Key Concepts

### Column-Based Calculations
All calculations in Glide are built by creating computed columns that build on each other, not cell formulas. Chain columns together for complex calculations.

### AI Columns
Glide has powerful AI columns that run inference on your data:
- **Generate Text** - Create summaries, descriptions, recommendations
- **Image to Text** - Extract info from photos (receipts, documents, products)
- **Document to Text** - Parse and summarize PDFs and documents
- **Text to Choice/Boolean** - Auto-categorize and classify content

Look for opportunities to add AI features to make apps more useful!

### Big Tables vs Native Tables
- **Native Tables**: Per-app, can't be shared, full computed column support
- **Big Tables**: Team-level, can be shared across apps, required for API access

### Shared Tables
Link the same Big Table to multiple apps by clicking your team name in Data Sources section.

### Access Settings
Configure privacy (Private/Public), authentication methods, and Row Owners for data-level security.

## Documentation

- [Glide Documentation](https://www.glideapps.com/docs)
- [Glide API Reference](https://apidocs.glideapps.com/api-reference/v2/general/introduction)
- [@glideapps/tables npm](https://www.npmjs.com/package/@glideapps/tables)

## Shortcuts

| Shortcut | Action |
|----------|--------|
| `Cmd+K` | Search in builder |
| `Cmd+Shift+Enter` | Data operations shortcut |

## Tips

- **Data first, then screens** - create tables before building the UI
- **Screen = Table + Collection + Details** - each screen is backed by a table
- **Calculations are columns** - add computed columns, not cell formulas
- **Do not use the Agent tab** - there's an "Agent" tab in Glide Builder, but it's prone to errors. Build manually with Data/Layout tabs instead
- Visibility conditions are NOT security - use Row Owners for data protection
- Test as different users with "Viewing as" dropdown before publishing
