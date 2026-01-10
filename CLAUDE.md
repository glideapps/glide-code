# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Repository Overview

This is a **Glide app builder plugin** that uses browser automation to create Glide apps at go.glideapps.com.

## ⚠️ START HERE

When building a Glide app, **read these files first:**

1. **`glide/orchestrator/ORCHESTRATOR.md`** - Main workflow coordinator
2. **`glide/CLAUDE.md`** - Plugin overview and architecture

## Architecture

This plugin uses a **three-layer hierarchical architecture:**

```
ORCHESTRATOR (glide/orchestrator/)
    │
    ├── data-agent (glide/agents/data/)
    │       └── procedures/ (step-by-step browser commands)
    │
    ├── screen-builder (glide/agents/screen-builder/)
    │       └── procedures/
    │
    ├── component-builder (glide/agents/component-builder/)
    │       └── procedures/
    │
    └── BROWSER EXECUTOR (glide/browser/EXECUTOR.md)
            └── selectors/ (UI element locations)
```

**Key principle:** 
- Orchestrator knows WHAT to do (phases, coordination)
- Sub-agents know HOW to do it (procedures)
- Browser executor just executes commands (no Glide knowledge)

## Key File Paths

| Purpose | Path |
|---------|------|
| **Orchestrator** | `glide/orchestrator/ORCHESTRATOR.md` |
| **Plugin Overview** | `glide/CLAUDE.md` |
| **Data Agent** | `glide/agents/data/AGENT.md` |
| **Screen Builder** | `glide/agents/screen-builder/AGENT.md` |
| **Component Builder** | `glide/agents/component-builder/AGENT.md` |
| **Browser Executor** | `glide/browser/EXECUTOR.md` |

## Procedures (How-To)

Step-by-step browser automation guides:

```
glide/agents/data/procedures/
  - add-column.md, add-relation.md, add-computed.md, get-api-key.md

glide/agents/screen-builder/procedures/
  - create-app.md, create-tab.md, set-app-settings.md

glide/agents/component-builder/procedures/
  - configure-collection.md, add-component.md, add-action.md
```

## Skills (Domain Knowledge)

Reference material about Glide features:

```
glide/skills/
  - data-modeling/, computed-columns/, design/, api/, etc.
```

## Browser Selectors

UI element locations:

```
glide/browser/selectors/
  - navigation.md, data-editor.md, components.md
```

## Build Workflow Summary

1. **Clarify** - Understand what user wants
2. **Setup** - Create blank app, get API key
3. **Data** - Create tables via API
4. **Screens** - Add tabs from tables
5. **Components** - Configure collections, detail screens, forms
6. **Polish** - Actions, settings, branding
7. **Review** - Design review
8. **QA** - Verify features work

## Commands

| Command | Description |
|---------|-------------|
| `/glide` or `/start` | Guided setup |
| `/ask` | Expert advisor - answer questions, assess viability, plan apps |
| `/email-success` | Draft email responses to customer Glide questions |
| `/tip` | Provide expert guidance - plugin learns |

## Key Rules

1. **Data before screens** - Tables must exist before building UI
2. **Use API for table creation** - Faster than UI
3. **Never "Import a file"** - Always Blank app + API
4. **App names: 2 words max** - Short, no company name
5. **Always include images** - Use picsum.photos placeholders
