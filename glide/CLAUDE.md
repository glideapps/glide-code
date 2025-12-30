# Glide Plugin - Important Guidelines

See the `glide` skill for detailed workflow and builder documentation. **Read it first when building any Glide app.**

## Build Workflow Summary

1. **Clarify use case** - Confirm what the user wants, or ask if unclear
2. **Create app first** - Get browser auth done early (don't make user wait during file analysis)
3. **Analyze file** (if provided) - Use `file-analysis` agent with context hints
4. **Create tables via API** - Use `data` agent (faster than UI, always create NEW tables)
5. **Build basic screens** - Use `builder` agent for Playwright automation
6. **Design review** - Use `design` agent to improve screens (layout, components, data density)
7. **QA verification** - Use `qa` agent to verify features actually work before telling user it's ready
8. **Finalize** - Access settings, testing, publish

## Parallelization

**Glide is multiplayer** - the same app can be open in multiple browser tabs simultaneously.

**Each agent gets its own tab**: When `builder`, `data`, or `design` agents need to use the browser, they should open their own exclusive tab. This prevents agents from contending with each other.

Use this to parallelize work:
- Each agent opens its own tab with `browser_tabs`
- Run parallel agents on different tasks
- Agents work independently without interfering

Examples:
- **Parallel screens**: Tab 1 builds Screen A, Tab 2 builds Screen B
- **Parallel columns**: Open the same table in multiple tabs, create a different computed column in each tab simultaneously
- **Data + UI**: Tab 1 loads data via API, Tab 2 configures branding
- **Parallel table imports**: Spawn multiple `data` agents, one for each table to import. Each agent opens its own tab, gets the API token, creates its table, and links it to the app.

This can significantly speed up app building.

## Files → file-analysis Agent

**ALWAYS use `file-analysis` first** when user provides a spreadsheet, CSV, or data file.

Pass context hints:
- What kind of app they want
- Key features mentioned
- Who will use it

The file-analysis agent returns:
- Data to import (tables, columns)
- Relationships (→ relation columns)
- Formulas to rebuild (→ computed columns)

## Agent Responsibilities

- **builder**: Creates app first (browser auth), then builds via Playwright
- **file-analysis**: Analyzes files, creates specifications (data, relationships, formulas)
- **data**: Creates tables and imports data via Glide API
- **design**: Reviews screens after building, suggests improvements to layout/components/data density
- **qa**: Verifies features actually work before telling user the app is ready

## Key Rules

1. **Always create Blank apps** - NEVER use "Import a file" option
2. **Create app first** - Get browser auth done early, don't make user wait
3. **Files → file-analysis agent** - Don't skip this step; pass context hints
4. **Create NEW tables** - Don't inject data into existing tables
5. **Use API for data import** - Don't use Glide's UI file upload
6. **Always include images** - Use picsum.photos placeholders
7. **Don't use the Agent tab** in Glide Builder - it's error-prone
