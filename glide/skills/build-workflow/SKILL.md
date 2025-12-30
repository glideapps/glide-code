---
description: |
  How to build Glide apps - the workflow, agent coordination, and key patterns.
  Use this skill when building any Glide app to understand the process.
---

# Glide Build Workflow

## Overview

Building a Glide app involves coordinating multiple agents. This skill covers the workflow and when to use each agent.

## Parallelization (Speed Up Builds)

**Glide is multiplayer** - multiple users (or agents) can edit the same app simultaneously.

Use this to parallelize work:
- Open the same app in **multiple browser tabs**
- Run parallel agents working on different tasks
- Use `browser_tabs` Playwright tool to create/switch tabs

Example parallel workflows:
- Tab 1: `data` agent creating tables and loading data
- Tab 2: `builder` agent setting up branding and navigation
- Tab 1: `builder` agent working on Screen A
- Tab 2: `builder` agent working on Screen B
- **Parallel columns**: Open the same table in multiple tabs, create a different computed column in each tab simultaneously
- **Parallel table imports**: Spawn multiple `data` agents (one per table). Each opens its own tab, gets the API token if needed, creates its table via API, and links it to the app.

This can significantly speed up complex app builds.

## Agents

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| `builder` | Browser automation via Playwright | Creating apps, adding screens, configuring UI |
| `file-analysis` | Analyze spreadsheets/data files | User provides a file to build from |
| `data` | Glide API operations | Creating tables, importing data, bulk operations |
| `design` | Review and improve screens | After basic screens are built, to refine them |

## Build Workflow

### Step 1: Clarify Use Case

**Before doing anything, understand what the user wants.**

- If clear: Confirm back to them ("I'll build a task management app for your team")
- If unclear: Ask briefly what the app should do, who will use it

This context is critical for:
- Naming the app
- Guiding file analysis
- Knowing what screens and features to build

### Step 2: Create App First (Browser Auth)

**Create the app in Glide immediately**, before any file analysis.

Why:
- Gets browser authentication out of the way early
- User sees progress right away
- Creates a placeholder to build into

Use the `builder` agent to:
1. Navigate to go.glideapps.com
2. Handle login if needed (ask user to sign in)
3. Click "New app" → select **"Blank"** → "Create app"

**NEVER use "Import a file"** - always start with a blank app. We import data via API later.

### Step 3: Analyze File (If User Has One)

**If the user provides a spreadsheet or data file:**

Use the `file-analysis` agent. Pass it:
- The file path
- Context hints (app purpose, key features, who will use it)

The file-analysis agent returns:
1. **Data to Import** - Tables, columns, types, row counts
2. **Relationships** - What connects to what (becomes relation columns)
3. **Formulas to Rebuild** - Calculations to recreate as computed columns

**Do NOT analyze files directly** - the file-analysis agent uses specialized tools.

### Step 4: Create Tables via API

Use the `data` agent to create tables and import data.

Why API instead of UI:
- Much faster for bulk data
- Cleaner than file upload UI
- Can create multiple tables quickly

**Critical**: Always create NEW tables. Never inject data into existing tables.

**Always include images**: Use `https://picsum.photos/seed/{unique-id}/400/300` for placeholders.

### Step 5: Build in Glide

Use the `builder` agent to:
1. Set up branding (name, icon, colors) - quick win
2. Link the tables you created via API
3. Add computed columns (relations, rollups, math, AI columns)
4. Create screens from data
5. Basic configuration of collections and detail views

### Step 6: Design Review (Important!)

**After basic screens are in place, use the `design` agent to improve them.**

The design agent will:
1. Take screenshots of each screen
2. Analyze layout, components, and data density
3. Suggest improvements based on use case
4. Research Glide docs for better component options

Design improvements to look for:
- Status emojis via If-Then-Else columns
- Hero Icons for visual indicators
- Inline collections for multi-relations
- Number formatting (decimals, units, prefixes)
- Better component choices for data types

Then use `builder` to implement the suggested improvements.

### Step 7: Finalize

Use the `builder` agent to:
1. Configure access and privacy settings
2. Test as different users
3. Publish when ready

## Key Patterns

### Screen = Table + Collection + Detail

Every screen in Glide follows this pattern:
- **Backing Table**: Provides the data
- **Collection View**: Shows list/cards/table of items
- **Detail View**: Shows one item when clicked

### Calculations are Columns

Glide doesn't have cell formulas. All calculations are **computed columns** that apply to every row:
- Math columns for arithmetic
- If-Then-Else for conditional logic
- Rollups for aggregations across relations
- Lookups for pulling data from related rows

### AI Columns

Look for opportunities to add AI features:
- Generate Text for summaries/descriptions
- Image to Text for receipt/document scanning
- Text to Choice for auto-categorization

## What NOT to Do

- **Don't use "Import a file"** when creating apps - always start with Blank
- **Don't use the Agent tab** in Glide Builder - it's error-prone
- **Don't use Glide's file upload UI** - use the API instead
- **Don't inject into existing tables** - create new ones
- **Don't skip images** - apps without images look broken
