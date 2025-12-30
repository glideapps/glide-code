---
description: |
  Bypass the Glide UI by directly invoking Redux actions via JavaScript.
  Use this for fast app modifications without clicking through the UI.
---

# Direct JavaScript Mutations in Glide Builder

## Overview

Glide Builder uses Redux for state management. By accessing the Redux store directly, we can dispatch actions to modify apps instantly without navigating through the UI.

**When to use this technique:**
- Changing app settings (title, description, icon)
- Bulk operations that would require many UI clicks
- When the UI is slow or unresponsive
- For operations where we know the exact data structure

**When NOT to use this:**
- Complex operations where UI feedback is helpful
- When you need to see visual results immediately
- Operations that require selecting from dynamic lists (like linking tables)

## Setup: Inject the Helper

Before using mutations, inject the `glideMutate` helper into the page:

```javascript
// Run this via browser_evaluate first
() => {
  // Find Redux store
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
          window.__glideStore = store;
          break;
        }

        if (fiber.child) queue.push(fiber.child);
        if (fiber.sibling) queue.push(fiber.sibling);
      }

      if (store) break;
    }
  }

  if (!store) return { error: "Store not found" };

  // Get webpack require
  const chunks = window.webpackChunk_glide_app;
  let webpackRequire = null;
  chunks.push([[Symbol()], {}, (req) => { webpackRequire = req; }]);

  const actionModule = webpackRequire("96969");

  // Create helper
  window.glideMutate = {
    getAppID: () => {
      const match = window.location.pathname.match(/\/app\/([^\/]+)/);
      return match ? match[1] : null;
    },
    getStore: () => store,
    getApp: (appID) => {
      appID = appID || window.glideMutate.getAppID();
      return store.getState().apps[appID];
    },
    updateApp: (updates, appID) => {
      appID = appID || window.glideMutate.getAppID();
      const action = actionModule.updateSerializedApp(appID, updates);
      store.dispatch(action);
      return store.getState().apps[appID]?.serializedApp;
    },
    addTab: (tabDescription, index, appID) => {
      appID = appID || window.glideMutate.getAppID();
      const action = actionModule.addTab(appID, tabDescription, index);
      store.dispatch(action);
    },
    removeTab: (tabID, appID) => {
      appID = appID || window.glideMutate.getAppID();
      const action = actionModule.removeTab(appID, tabID);
      store.dispatch(action);
    },
    dispatch: (action) => store.dispatch(action),
    actions: actionModule
  };

  return { success: true, appID: window.glideMutate.getAppID() };
}
```

## Common Operations

### Change App Title

```javascript
() => {
  window.glideMutate.updateApp({ title: "My New App Name" });
  return { success: true };
}
```

### Change App Description

```javascript
() => {
  window.glideMutate.updateApp({
    title: "My App",
    description: "A description of what this app does"
  });
  return { success: true };
}
```

### Get Current App State

```javascript
() => {
  const app = window.glideMutate.getApp();
  return {
    title: app.serializedApp?.title,
    tabs: app.serializedApp?.tabs?.length,
    theme: app.serializedApp?.theme
  };
}
```

## Available Actions

The `window.glideMutate.actions` object contains all Redux action creators:

| Action | Purpose |
|--------|---------|
| `updateSerializedApp` | Update app settings (title, description, etc.) |
| `addTab` | Add a new screen/tab |
| `removeTab` | Remove a screen/tab |
| `updateTabDescription` | Modify a tab's properties |
| `updateComponentDescription` | Modify a component's properties |
| `updateTheme` | Change theme settings |
| `updateAppFeatures` | Toggle feature flags |
| `reorderTabs` | Change tab order |
| `reorderComponent` | Change component order |

## How It Works

1. **Redux Store**: Glide uses Redux for state management. The store is passed via React context to components.

2. **Action Creators**: Module `96969` exports all the action creator functions. These create action objects like `{ type: "UPDATE_SERIALIZED_APP", payload: {...} }`.

3. **Dispatch**: Calling `store.dispatch(action)` applies the change to Redux state and triggers React re-renders.

4. **Persistence**: The `AppMutationManager` listens for Redux changes and automatically persists them to Firestore with debouncing.

## Important Notes

- **Changes persist automatically**: Redux actions trigger the mutation manager which saves to Firestore.
- **Module ID may change**: The webpack module ID (`96969`) could change between Glide deployments. If mutations stop working, search for a module exporting `updateSerializedApp`.
- **UI updates instantly**: React re-renders immediately after dispatch, so the UI reflects changes.
- **No undo through this method**: Direct dispatches bypass the undo manager. Use the UI for operations where undo is important.

## Combining with UI Automation

For best results, use JS mutations for:
- Setting app name, description, icon
- Bulk property changes

Use UI automation (Playwright) for:
- Creating screens from data (needs table picker)
- Adding components (needs component picker)
- Linking Big Tables (needs table browser)
- Operations requiring visual confirmation
