---
name: builder
description: |
  Build Glide apps using browser automation and direct JavaScript mutations.
  Use when creating apps, navigating the Builder UI, adding screens/components, or modifying app settings.
---

# Glide Builder

This skill covers building Glide apps via Playwright browser automation and fast JavaScript mutations.

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

### Adding Columns
1. Click on any column header → "Add column right"
2. Type the column **Name**
3. **Always explicitly select the Type** from dropdown (even for Text)
4. Configure options and click **Save**

**Important**: Interacting with the type picker commits the column name. If you skip this step, the name may not save.

### Column Type Categories
- **Basic** - Text, Number, Boolean, Image, Date & Time
- **Computed** - Math, Template, If-Then-Else, Relations
- **AI** - Audio to Text, Generate Text, Text to JSON
- **Integrations** - Call API, Claude, OpenAI, Google Maps

The dropdown is searchable - type to filter.

## Layout Editor

### Adding Screens
1. Click "+" in Navigation or Menu section
2. Choose: **Screen from data**, **Custom screen**, or **Form screen**
3. Configure in right panel

### Adding Components
1. Select a screen
2. Click "+" in Components section
3. Search or browse categories
4. Click to add, configure in right panel

### Collection Styles
Card, List, Table, Data Grid, Checklist, Calendar, Kanban, Custom

## Settings

**Name & Icon** - Title, icon, logo, description
**Appearance** - Accent color, layout (Left/Top), style (Light/Deep/Accent)
**Access** - Who can access the app
**Integrations** - External connections

---

## Direct JavaScript Mutations

For simple settings changes, bypass the UI entirely by dispatching Redux actions via `browser_evaluate`. Much faster for bulk operations.

**What works:** App settings, screens, components, tabs, theme
**What doesn't work:** Tables and columns (they use Firebase Cloud Functions, not Redux)

### Setup: Inject the Helper

Run this via `browser_evaluate` before using mutations:

```javascript
() => {
  const allElements = document.querySelectorAll('*');
  let store = null;
  for (const el of allElements) {
    const containerKey = Object.keys(el).find(k => k.startsWith('__reactContainer$'));
    if (containerKey) {
      const root = el[containerKey];
      const queue = [root];
      let iterations = 0;
      while (queue.length > 0 && iterations < 500) {
        iterations++;
        const fiber = queue.shift();
        if (!fiber) continue;
        if (fiber.memoizedProps?.store?.dispatch && fiber.memoizedProps?.store?.getState) {
          store = fiber.memoizedProps.store;
          break;
        }
        if (fiber.child) queue.push(fiber.child);
        if (fiber.sibling) queue.push(fiber.sibling);
      }
      if (store) break;
    }
  }
  if (!store) return { error: "Store not found" };

  const chunks = window.webpackChunk_glide_app;
  let webpackRequire = null;
  chunks.push([[Symbol()], {}, (req) => { webpackRequire = req; }]);
  const actionModule = webpackRequire("96969");

  window.glideMutate = {
    getAppID: () => window.location.pathname.match(/\/app\/([^\/]+)/)?.[1],
    getApp: (appID) => store.getState().apps[appID || window.glideMutate.getAppID()],
    updateApp: (updates, appID) => {
      appID = appID || window.glideMutate.getAppID();
      store.dispatch(actionModule.updateSerializedApp(appID, updates));
      return { success: true };
    },
    dispatch: (action) => store.dispatch(action),
    actions: actionModule
  };
  return { success: true, appID: window.glideMutate.getAppID() };
}
```

### Common Operations

```javascript
// Change app title
() => { window.glideMutate.updateApp({ title: "My App Name" }); return { success: true }; }

// Change app icon (emoji)
() => { window.glideMutate.updateApp({ iconImage: { emoji: "📋" } }); return { success: true }; }

// Inspect app structure
() => {
  const app = window.glideMutate.getApp();
  return {
    title: app.serializedApp?.title,
    tabs: app.serializedApp?.tabs?.length,
    screens: Object.keys(app.serializedApp?.screenDescriptions || {})
  };
}
```

### Available Actions

| Action | Parameters | Purpose |
|--------|------------|---------|
| `updateSerializedApp` | (appID, updates) | Update app settings |
| `addTab` | (appID, tabDescription, index?) | Add navigation tab |
| `removeTab` | (appID, tabID) | Remove tab |
| `updateTabDescription` | (appID, screenName, tabDesc) | Update tab |
| `updateScreenDescription` | (appID, screenName, updates, allowNew) | Update screen |
| `updateComponentDescription` | (appID, screenName, componentID, newDesc) | Update component |
| `updateTheme` | (appID, theme) | Update branding |

### Data Structures

**PropertyDescription** - Most values are wrapped:
```javascript
{ kind: "column", value: "columnName" }  // Reference to column
{ kind: "string", value: "Static text" } // Static string
```

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

**If stuck:**
1. Press Escape to clear overlays
2. Click a main navigation tab to reset
3. Take a screenshot to see current state
4. If completely stuck, refresh the page

## When to Use Each Approach

**JS Mutations:** App name, icon, description, bulk property changes, theme
**UI Automation:** Creating screens from data, adding components, linking tables
