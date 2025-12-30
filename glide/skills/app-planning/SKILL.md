---
name: app-planning
description: |
  **Read this first when building a Glide app.** Covers the build workflow, agent coordination, and parallelization.
  Use when starting any Glide app project to understand the process and which agents to use.
---

# Glide App Planning

**Start here when building a Glide app.** This skill covers the overall workflow and agent coordination.

## Build Workflow Summary

1. **Clarify use case** - Understand what the user wants
2. **Create app first** - Get browser auth done early (blank app)
3. **Analyze file** (if provided) - Use `file-analysis` agent
4. **Create tables via API** - Use `data` agent (faster than UI)
5. **Build screens** - Use `builder` agent for Playwright automation
6. **Design review** - Use `design` agent to improve screens
7. **Finalize** - Access settings, testing, publish

## Agents

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| `builder` | Browser automation via Playwright | Creating apps, adding screens, configuring UI |
| `file-analysis` | Analyze spreadsheets/data files | User provides a file to build from |
| `data` | Glide API operations | Creating tables, importing data, bulk operations |
| `design` | Review and improve screens | After basic screens are built |

## Parallelization

**Glide is multiplayer** - multiple agents can edit the same app simultaneously in different browser tabs.

Examples:
- Tab 1: `data` agent creating tables → Tab 2: `builder` setting up branding
- Tab 1: Screen A → Tab 2: Screen B
- Multiple `data` agents importing different tables in parallel

Use `browser_tabs` Playwright tool to create/switch tabs.

## Step-by-Step

### 1. Clarify Use Case

**Before doing anything, understand what the user wants.**

- If clear: Confirm back ("I'll build a task management app for your team")
- If unclear: Ask what the app should do, who will use it

### 2. Create App First

**Create the app immediately**, before file analysis.

Why:
- Gets browser auth out of the way early
- User sees progress right away

Use `builder` agent: go.glideapps.com → "New app" → **"Blank"** → "Create app"

**NEVER use "Import a file"** - always Blank. Data comes via API.

### 3. Analyze File (If User Has One)

Use `file-analysis` agent with:
- The file path
- Context hints (app purpose, key features, who will use it)

Returns: tables to import, relationships, formulas to rebuild

### 4. Create Tables via API

Use `data` agent - much faster than UI for bulk data.

**Critical**: Always create NEW tables. Never inject into existing ones.

**Always include images**: Use `https://picsum.photos/seed/{id}/400/300` placeholders.

### 5. Build Screens

Use `builder` agent to:
1. Set branding (name, icon, colors)
2. Link tables created via API
3. Add computed columns (relations, rollups, AI)
4. Create screens from data
5. Configure collections and detail views

### 6. Design Review

Use `design` agent after basic screens exist. It will:
- Screenshot each screen
- Suggest improvements (status emojis, icons, better components)
- Research Glide docs for options

Then use `builder` to implement improvements.

### 7. Finalize

Configure access/privacy, test as different users, publish.

## Key Patterns

**Screen = Table + Collection + Detail**
- Backing table provides data
- Collection shows list/cards
- Detail shows one item

**Calculations are Columns**
- Math for arithmetic
- If-Then-Else for conditions
- Rollups for aggregations
- Lookups for related data

## What NOT to Do

- Don't use "Import a file" - always Blank
- Don't use the Agent tab in Glide Builder
- Don't use Glide's file upload UI - use API
- **Don't inject into existing tables** - always create NEW tables
- **NEVER import into the Users table** - it's reserved for user profiles only
- Don't click inline "Import" links next to tables - use "+" menu instead
- Don't skip images
