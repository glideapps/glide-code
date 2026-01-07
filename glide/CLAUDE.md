# Glide Plugin

Build Glide apps using a hierarchical agent system with browser automation.

## ⚠️ IMPORTANT: Start with the Orchestrator

**When building any Glide app, FIRST read:**
```
glide/orchestrator/ORCHESTRATOR.md
```

This is the main coordinator. Do NOT try to build everything in one agent.

## Architecture Overview

This plugin uses a **full-stack agent architecture**:

```
┌─────────────────────────────────────────────────────────────────┐
│                        ORCHESTRATOR                              │
│  Coordinates build workflow, delegates to specialized agents     │
│  READ: orchestrator/ORCHESTRATOR.md                             │
└───────────────────────┬─────────────────────────────────────────┘
                        │
        ┌───────────────┴───────────────┬───────────────┬─────────┐
        ▼                               ▼               ▼         ▼
┌───────────────────────┐  ┌────────────────────┐ ┌─────────┐ ┌────────┐
│      DATA AGENT       │  │   SCREEN BUILDER   │ │ DESIGN  │ │   QA   │
│     (Backend Stack)   │  │   (Frontend Stack) │ │ REVIEW  │ │ AGENT  │
│                       │  │                    │ │         │ │        │
│ • Tables & Columns    │  │ • Apps & Settings  │ │ Audit,  │ │ Test,  │
│ • Relations           │  │ • Tabs & Screens   │ │ Score   │ │ Verify │
│ • Computed Columns    │  │ • Components       │ │         │ │        │
│ • API Operations      │  │ • Actions          │ │         │ │        │
└───────────┬───────────┘  └──────────┬─────────┘ └────┬────┘ └───┬────┘
            │                         │                │          │
            └─────────────────────────┴────────────────┴──────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                     BROWSER EXECUTOR                             │
│  Pure browser automation - click, type, navigate                 │
│  READ: browser/EXECUTOR.md                                      │
└─────────────────────────────────────────────────────────────────┘
```

## How It Works

1. **Orchestrator** receives the user's request and breaks it into phases
2. **Sub-agents** have deep domain knowledge and know HOW to do things
3. **Browser Executor** performs atomic browser commands (no Glide knowledge)

This separation means:
- Orchestrator focuses on WHAT needs to be done
- Sub-agents focus on HOW to do their specialty
- Browser Executor focuses on executing commands reliably

## Quick Start

### For New Apps

Say: "Build me a [type of app] in Glide"

The orchestrator will:
1. Clarify requirements
2. Create a blank app
3. Set up data tables
4. Build screens
5. Configure components
6. Review and polish

### For Existing Apps

Say: "Help me add [feature] to my Glide app at [URL]"

The orchestrator will delegate to the appropriate sub-agent.

## Sub-Agents

| Agent | Stack | Location | Responsibility |
|-------|-------|----------|----------------|
| **data-agent** | Backend | `agents/data/` | Tables, columns, relations, computed columns, API |
| **screen-builder** | Frontend | `agents/screen-builder/` | Apps, tabs, screens, components, actions |
| **workflows-agent** | Automation | `agents/workflows/` | Workflows, triggers, loops, conditions, integrations |
| **design-review** | QA | `agents/design-review.md` | Critique screens, suggest improvements |
| **qa-agent** | QA | `agents/qa.md` | Verify features work before delivery |

## Procedures

Each sub-agent has **procedures** - step-by-step browser automation scripts organized in subdirectories:

```
agents/
├── data/                          ← Backend Stack
│   └── procedures/
│       ├── add-column.md          # Basic column types
│       ├── add-relation.md        # Table relations
│       ├── get-api-key.md         # API token retrieval
│       └── computed/              # Computed columns
│           ├── math.md
│           ├── if-then-else.md
│           ├── template.md
│           ├── lookup.md
│           └── rollup.md
│
├── screen-builder/                ← Frontend Stack
│   └── procedures/
│       ├── app/                   # App-level operations
│       │   ├── create-app.md
│       │   └── set-app-settings.md
│       ├── navigation/            # Navigation structure
│       │   └── create-tab.md
│       └── components/            # UI components
│           ├── configure-collection.md
│           ├── add-component.md
│           └── add-action.md
│
└── workflows/                     ← Automation Stack
    └── procedures/
        ├── triggers/              # Workflow trigger types
        │   ├── create-schedule.md
        │   ├── create-webhook.md
        │   ├── create-manual.md
        │   ├── create-email.md
        │   └── create-slack.md
        └── nodes/                 # Workflow nodes
            ├── add-loop.md
            ├── add-condition.md
            ├── add-query-json.md
            └── trigger-workflow.md
```

Procedures are the source of truth for HOW to do things in the Glide UI.

## Skills (Domain Knowledge)

Skills describe WHAT Glide features exist (reference material):

| Skill | Description |
|-------|-------------|
| `data-modeling` | Tables, columns, data types |
| `computed-columns` | Math, If-Then-Else, Templates |
| `design` | Screen design principles, component selection |
| `components` | All component types and options |
| `collections` | Collection styles and when to use each |
| `api` | Glide API v2 for data operations |

## Browser Selectors

Reference for finding UI elements:

```
browser/selectors/
├── navigation.md      # Main nav, dashboard, app creation
├── data-editor.md     # Tables, columns, API panel
└── components.md      # Component picker, configuration
```

## Key Rules

1. **Data before screens** - Tables must exist before building UI
2. **Use API for table creation** - Much faster than UI
3. **Never "Import a file"** - Always use Blank app + API
4. **App names: 2 words max** - Short, no company name, no punctuation
5. **Always include images** - Use picsum.photos placeholders
6. **Don't use Agent tab** - Build manually with Data/Layout tabs

## Commands

| Command | Description |
|---------|-------------|
| `/glide` | Build apps - guided setup, login, app selection, building |
| `/ask` | Expert advisor - viability assessment, planning, feature questions |
| `/tip` | Provide expert guidance - plugin learns and updates |

## Build Phases (Orchestrator)

1. **Clarify** - Understand what user wants
2. **Setup** - Create blank app, get API key
3. **Data** - Create tables, columns, relations
4. **Screens** - Add tabs from tables
5. **Components** - Configure collections, detail screens, forms
6. **Polish** - Actions, settings, branding
7. **Workflows** - Automations, integrations (if needed)
8. **Review** - Design review agent critiques
9. **QA** - Verify features actually work

## Multi-Browser Support

6 browser instances available for parallel work:
- `browser-1` through `browser-6`
- Each has its own profile
- Login once, profiles sync to all browsers

## Documentation

- [Glide Docs](https://www.glideapps.com/docs)
- [Glide API](https://apidocs.glideapps.com/api-reference/v2/general/introduction)
