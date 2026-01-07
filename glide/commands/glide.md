---
name: glide
description: Start working with Glide - includes interactive login, app selection, and task choice
---

# Glide Start Command

This command guides you through the initial setup for working with Glide apps.

## ⚠️ FIRST: Read the Orchestrator

**Before doing anything, read the orchestrator documentation:**

```
orchestrator/ORCHESTRATOR.md
```

This is the central coordination point that explains:
- Build phases and workflow
- How to delegate to sub-agents
- What each agent is responsible for

## Quick Start Flow

### Step 1: Check Authentication

Use browser to check if logged in:

```
1. Navigate to https://go.glideapps.com
2. Take snapshot
3. If "Sign In" visible → User needs to log in
4. If "New app" visible → Already authenticated
```

If not logged in, tell the user:
> "Please log in to Glide in the browser window. I'll detect when you're done."

### Step 2: Determine Task

Ask the user what they want to do:
- **"Build a new app"** → Start with orchestrator Phase 1 (Clarify)
- **"Work on existing app"** → Ask for app URL, then determine what to do
- **"Import data"** → Delegate to data-agent
- **"Review design"** → Delegate to design-review agent

### Step 3: Delegate to Orchestrator

For any building task, follow the orchestrator's phases:

1. **Clarify** - Understand requirements
2. **Setup** - Create blank app (delegate to screen-builder)
3. **Data** - Create tables (delegate to data-agent)
4. **Screens** - Build UI (delegate to screen-builder, component-builder)
5. **Polish** - Actions, settings
6. **Review** - Design review
7. **QA** - Verify features work

## Key Files to Reference

| Purpose | File Path |
|---------|-----------|
| Main orchestrator | `glide/orchestrator/ORCHESTRATOR.md` |
| Data operations | `glide/agents/data/AGENT.md` |
| Screen building | `glide/agents/screen-builder/AGENT.md` |
| Component config | `glide/agents/component-builder/AGENT.md` |
| Browser executor | `glide/browser/EXECUTOR.md` |

## Sub-Agent Procedures

Each agent has step-by-step procedures:

```
glide/agents/data/procedures/
  - add-column.md
  - add-relation.md
  - add-computed.md
  - get-api-key.md

glide/agents/screen-builder/procedures/
  - create-app.md
  - create-tab.md
  - set-app-settings.md

glide/agents/component-builder/procedures/
  - configure-collection.md
  - add-component.md
  - add-action.md
```

## Browser Tools

Use the browser MCP tools for automation:
- `mcp_cursor-browser-extension_browser_navigate` - Go to URL
- `mcp_cursor-browser-extension_browser_snapshot` - Get page state
- `mcp_cursor-browser-extension_browser_click` - Click elements
- `mcp_cursor-browser-extension_browser_type` - Type text

## Tracking App Context

Keep track of these values during a build session:
- **App ID**: Extract from URL (`go.glideapps.com/app/{APP_ID}/layout`)
- **API Key**: Copy from Data Editor → Show API → Copy secret token

Pass these to sub-agents when delegating tasks.
