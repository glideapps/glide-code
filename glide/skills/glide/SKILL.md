---
name: glide
description: |
  **The primary skill for building Glide apps.** Covers the full workflow, agent coordination, and browser automation.
  Use when building, creating, or modifying any Glide app. Read this first for any Glide project.
---

# Glide App Building

**Start here when building a Glide app.** This skill covers the overall workflow, agent coordination, and technical details for browser automation.

## Build Workflow Summary

1. **Clarify use case** - Understand what the user wants
2. **Create app first** - Get browser auth done early (blank app)
3. **Analyze file** (if provided) - Use `file-analysis` agent
4. **Create tables via API** - Use `data` agent (faster than UI)
5. **Build screens** - Use `build` agent for Playwright automation
6. **Design review** - Apply `design` skill to evaluate and improve screens
7. **QA verification** - Use `qa` agent to verify features actually work
8. **Finalize** - Access settings, testing, publish

## Agents

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| `build` | Browser automation via Playwright | Creating apps, adding screens, configuring UI |
| `file-analysis` | Analyze spreadsheets/data files | User provides a file to build from |
| `data` | Glide API operations | Creating tables, importing data, bulk operations |
| `design-review` | Critique screens for design quality | After building screens, to improve component choices and layouts |
| `qa` | Verify features actually work | Before telling user the app is ready |
| `app-research` | Explore and document existing apps | Understanding an app's structure, answering questions about it |

**Skills:**
| Skill | Purpose | When to Use |
|-------|---------|-------------|
| `design` | Screen design principles, techniques, and component guidance | Evaluating collection styles, improving layouts, choosing components |

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

Use `build` agent: go.glideapps.com → "New app" → **"Blank"** → "Create app"

**NEVER use "Import a file"** - always Blank. Data comes via API.

### 3. Analyze File (If User Has One)

Use `file-analysis` agent with:
- The file path
- Context hints (app purpose, key features, who will use it)

Returns: tables to import, relationships, formulas to rebuild

### 4. Create Tables via API

**When you need tables (either from a file or for a new app concept), use the `data` agent to create them via the API.**

This is the primary method for adding tables to Glide apps. Don't attempt to create tables through the UI.

**Workflow**:
1. **Design the data schema** - Determine what tables and columns are needed
2. **Use the `data` agent** - Pass table schemas and sample data
3. **The data agent creates tables via Glide API** - Tables are created and linked to the app
4. **Build agent links tables in UI** - Tables appear in Data Editor, ready to use

**Critical**: Always create NEW tables. Never inject into existing ones.

**Always include images**: Use `https://picsum.photos/seed/{id}/400/300` placeholders.

### 5. Build Screens

Use `build` agent to:
1. Set branding (name, icon, colors) - see naming rules below
2. Link tables created via API
3. Add computed columns (relations, rollups, AI)
4. Create screens from data
5. Configure collections and detail views

**Critical - Screen Creation Method:**
- **Never use Sample Screen templates** (Project management, Dashboard, etc.) - they make data integration difficult
- **Use "Screen from data"** for collections/lists backed by tables
- **Use "Custom Screen"** (optionally with "Choose data source") for dashboards
- **Always start blank and add components** - this gives full control over data binding and layout

**App naming rules:**
- **Short**: 2 words maximum
- **No company name**: The app lives in the company's team already
- **No punctuation**: No periods, exclamation marks, etc.
- Good: "Employees", "Task Tracker", "Inventory"
- Bad: "Tesla Employee Directory", "Acme Inc. Tasks."

### 6. Design Review

After basic screens exist, use the `design-review` agent to critique and improve them:
- Check mobile and desktop layouts
- Evaluate collection styles (Card vs List vs Table vs Kanban)
- Review component choices (Contact, Big Numbers, Progress, etc.)
- Suggest improvements (status emojis, icons, better data density)

Then use `build` to implement the recommended improvements.

### 7. QA Verification (Critical!)

**Don't tell the user the app is ready until you verify features actually work.**

Use `qa` agent to:
- Verify screens exist and have components (not empty)
- Verify data binding works (real data shows, not placeholders)
- Test forms actually save data (check Data Editor)
- Test buttons/actions actually work
- Verify computed columns have formulas configured
- Check that workflows are set up correctly

The QA agent will produce a report of PASS/FAIL for each feature. Fix any failures before declaring the app complete.

### 8. Finalize

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

---

# Builder Reference

## URLs

| URL | Purpose |
|-----|---------|
| `go.glideapps.com` | Dashboard - view/manage apps |
| `go.glideapps.com/app/{appId}/layout` | Layout Editor |
| `go.glideapps.com/app/{appId}/data` | Data Editor |
| `go.glideapps.com/app/{appId}/settings` | App Settings |

## Main Navigation Tabs

1. **Data** - Data Editor for tables and columns
2. **Layout** - Screen and component design
3. **Workflows** - Automation sequences
4. **Settings** - App configuration

**Warning**: There is also an "Agent" tab in the builder. **Do not use it.** Build manually using Data and Layout tabs instead.

## Creating a New App

1. Navigate to `go.glideapps.com`
2. Click "New app" card
3. Select **Blank** (recommended - use API for data import)
4. Click "Create app"

## Data Editor

### Adding Columns (Browser Automation Required)

**All column creation must be done via browser automation** - the Glide API does not support adding columns to existing tables.

**Recommended method** - Keyboard shortcut (fastest):
1. Navigate to Data tab
2. Click on the target table
3. Press **CMD+SHIFT+ENTER** (macOS) or **CTRL+SHIFT+ENTER** (Windows)
4. Type the column **Name**
5. **Explicitly select the Type** from dropdown (required - commits the name)
6. Configure options and click **Save**

**Alternative method** - UI clicks:
1. Click on any column header → "Add column right"
2. Type the column **Name**
3. **Always explicitly select the Type** from dropdown (even for Text)
4. Configure options and click **Save**

**Important**:
- Interacting with the type picker commits the column name. If you skip this step, the name may not save.
- The keyboard shortcut method is significantly faster and more reliable for automation.

### Column Type Categories
- **Basic** - Text, Number, Boolean, Image, Date & Time
- **Computed** - Math, Template, If-Then-Else, Relations
- **AI** - Audio to Text, Generate Text, Text to JSON
- **Integrations** - Call API, Claude, OpenAI, Google Maps

The dropdown is searchable - type to filter.

## Layout Editor

### Adding Screens/Tabs
1. Click "+" in Navigation or Menu section
2. Choose: **Screen from data**, **Custom screen**, or **Form screen**
3. Configure in right panel
4. **Set a short label** - 1-2 words maximum (e.g., "Tasks", "My Team")
5. **Pick an appropriate icon** - click the icon and choose one that matches the content

**Tab labels**: Keep them short! 1-2 words max.
- Good: "Tasks", "Team", "Settings", "Orders"
- Bad: "My Task List", "Team Member Directory"

**Tab icons**: Don't leave random icons. Choose icons that represent the screen's purpose:
- Tasks/Todos: checkmark, clipboard, list
- People/Users: person, people, user
- Settings: gear, cog, sliders
- Home/Dashboard: home, grid, chart
- Messages: chat, envelope, bell
- Calendar/Events: calendar, clock
- Files/Documents: folder, document, file
- Search: magnifying glass
- Add/Create: plus, add

### Adding Components
1. Select a screen
2. Click "+" in Components section
3. Search or browse categories
4. Click to add, configure in right panel

### Collection Styles
Card, List, Table, Data Grid, Checklist, Calendar, Kanban, Custom

## Settings

**Name & Icon** - App name (keep short, 2 words max, no company name), icon, logo
**Appearance** - Accent color, layout (Left/Top), style (Light/Deep/Accent)
**Access** - Who can access the app
**Integrations** - External connections

### Changing App Icon (Emoji Picker)

To change the app icon using the emoji picker:

1. Go to **Settings** tab → **Name & Icon** section
2. **Click the icon image** (not the Upload button) → Opens "Emoji Mart™" picker
3. **Type the emoji name in the search box** (e.g., "rocket", "checkmark", "clipboard")
4. **Click the emoji button** from the search results

**Important**: Always use the search box! Browsing categories is slow and error-prone. The search instantly filters to matching emojis.

Common emoji searches for app icons:
- Tasks: "checkmark", "clipboard", "list"
- People: "person", "people", "user"
- Business: "briefcase", "chart", "money"
- Home: "home", "house"
- Settings: "gear", "wrench"
- Calendar: "calendar", "clock"
- Messages: "chat", "envelope", "bell"
- Food: "pizza", "coffee", "burger"
- Travel: "car", "plane", "rocket"

---

## Browser Automation Tips

**Column names may not save**: Always select the column type explicitly.

**Avoid info icons (ⓘ)**: They trigger tooltips that can block the UI.

### Hide Tooltips

Rich tooltips with class `rich-tooltip-*` can block clicks and interfere with automation. Run this via `browser_evaluate` to hide them:

```javascript
() => {
  document.querySelectorAll('[class*="rich-tooltip"]').forEach(el => el.remove());
  return { removed: true };
}
```

Run this periodically if tooltips keep appearing, or after hovering over info icons accidentally.

**Keyboard shortcuts:**
- `Cmd+K` - Open search
- `Escape` - Close dialogs and panels
- `CMD+SHIFT+ENTER` - Add new column (when in Data Editor with table selected)
- `CMD+Z` (macOS) / `CTRL+Z` (Windows) - Undo last action

**Undo mistakes:**
Glide Builder supports standard undo functionality. If you accidentally delete a component, action, or make an unwanted change, immediately use `CMD+Z` (macOS) or `CTRL+Z` (Windows) to revert it.

**Common scenarios:**
- Deleted the wrong action → CMD+Z to restore it
- Removed a component by mistake → CMD+Z to bring it back
- Changed a setting incorrectly → CMD+Z to revert

**Important**: Undo only works for recent actions in the current session. If you navigate away or refresh, you may not be able to undo.

**If stuck:**
1. Press Escape to clear overlays
2. Click a main navigation tab to reset
3. Take a screenshot to see current state
4. If completely stuck, refresh the page
