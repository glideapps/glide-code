# Glide Claude Code Plugin

A comprehensive Claude Code plugin for building and managing Glide applications using browser automation and the Glide API.

## What is This?

This is a **Claude Code plugin** that extends Claude Code with specialized capabilities for building and managing Glide apps:

- **Browser Automation**: Automate Glide Builder UI interactions using Playwright
- **API Integration**: Manage Glide data programmatically via the Glide API v2
- **Intelligent Agents**: Autonomous agents for file analysis, app building, data operations, and design review
- **Comprehensive Skills**: In-depth knowledge about Glide features and best practices

## Installation

### Option 1: Install from GitHub (Recommended)

```bash
claude --plugin-repo glideapps/glide-claude-code
```

This downloads and installs the plugin directly from the GitHub repository.

### Option 2: Install from Local Directory

```bash
claude --plugin-dir /path/to/glide-claude-code/glide
```

Useful during development or if you want to modify the plugin locally.

### Option 3: Copy to Your Project

Copy the `glide` folder to your project's `.claude/plugins/` directory:

```bash
cp -r glide-claude-code/glide /your/project/.claude/plugins/
```

Claude Code will auto-discover the plugin.

## How to Use

Once installed, simply ask Claude to help with Glide:

```
"Build a task management app in Glide"
"Import this CSV into my Glide table"
"Analyze my spreadsheet and create a data model"
```

Claude will use the appropriate agents and skills to help you build and manage your Glide apps.

## What's Included

### Agents

- **builder** - Autonomously builds Glide apps via browser automation
- **data** - Manages data operations via the Glide API
- **file-analysis** - Analyzes spreadsheets and data files
- **design** - Reviews and improves app designs

### Skills

- **build-workflow** - Overall workflow and agent coordination
- **builder-navigation** - UI navigation patterns and shortcuts
- **data-modeling** - Tables, columns, relationships, and calculations
- **computed-columns** - Advanced computed column types
- **ai** - AI columns and AI-powered features
- **components** - Screens, layouts, and UI components
- **api** - Glide API v2 usage and data operations
- **access-management** - Privacy settings and authentication
- **js-mutations** - Fast direct app modifications via JavaScript

## Prerequisites

1. **Glide Account** - Sign in at [go.glideapps.com](https://go.glideapps.com)
2. **API Token** (optional) - For programmatic data operations via the API

## First Time Setup

1. Start Claude Code with the plugin installed
2. Ask Claude to build a Glide app
3. A browser window will open - sign in to Glide (first time only)
4. Your login will be remembered for future sessions

## Configuration

To store your Glide API token securely, create `.claude/glide.local.md`:

```markdown
# Glide Settings

## API Token
GLIDE_API_TOKEN=your-secret-token-here
```

Get your token from: Glide Builder > Data tab > Show API > Copy secret token

## Documentation

See the full documentation in the `glide/` folder:

- **glide/README.md** - Detailed plugin documentation
- **glide/CLAUDE.md** - Guidelines and best practices
- **glide/skills/** - Individual skill documentation
- **glide/agents/** - Agent system prompts and workflows

## Features

✅ Build complete Glide apps with natural language commands
✅ Automate data operations and imports via API
✅ Analyze spreadsheets and generate app specs
✅ Create computed columns and data models
✅ Add AI features with Glide AI columns
✅ Manage access control and authentication
✅ Review and optimize app designs

## Repository

- **GitHub**: [glideapps/glide-claude-code](https://github.com/glideapps/glide-claude-code)
- **Issue Tracking**: [GitHub Issues](https://github.com/glideapps/glide-claude-code/issues)

## Contributing

This is a private Glide team repository. For issues or contributions, please contact the Glide team.

## License

Copyright © 2024 Glide. All rights reserved.
