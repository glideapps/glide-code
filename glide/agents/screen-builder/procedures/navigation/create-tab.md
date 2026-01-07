---
name: create-tab
description: Create a new navigation tab from a data table
preconditions:
  - App exists
  - Source table exists and is linked to app
---

# Create Tab Procedure

## Overview

Creates a new tab in the app navigation, backed by a data table. This creates a screen that shows a collection of items from the table.

## Procedure

### Step 1: Navigate to Layout Editor

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/layout

EXPECTED: Layout Editor loads
VERIFY: Left sidebar shows Navigation and Menu sections
```

### Step 2: Find the Add Button

```
COMMAND: SNAPSHOT
PURPOSE: Find the Navigation section and add button

FIND: In left sidebar:
      - Section labeled "Navigation" or "Tabs"
      - A "+" button to add new screens
      
NOTE: There may be two sections:
      - "Navigation" (main tabs)
      - "Menu" (slide-out menu)
      
      We usually want "Navigation" for main screens
```

### Step 3: Click Add (+) Button

```
COMMAND: CLICK
REF: {ref of + button in Navigation section}
ELEMENT: Add screen button in Navigation

EXPECTED: Screen type selection menu appears
```

### Step 4: Select "Screen from data"

```
COMMAND: SNAPSHOT
PURPOSE: Find screen type options

FIND: Menu showing options like:
      - "Screen from data" (what we want)
      - "Custom screen"
      - "Form screen"
```

```
COMMAND: CLICK
REF: {ref of "Screen from data" option}
ELEMENT: "Screen from data" option

EXPECTED: Table selection UI appears
```

### Step 5: Select Source Table

```
COMMAND: SNAPSHOT
PURPOSE: Find table list

FIND: List of available tables
      Should show all tables linked to this app
```

```
COMMAND: CLICK
REF: {ref of target table}
ELEMENT: "{tableName}" in table list

EXPECTED: 
  - Table selected
  - New tab created in Navigation
  - Preview shows collection of items from table
```

### Step 6: Configure Tab Label

```
COMMAND: SNAPSHOT
PURPOSE: Find tab configuration panel

FIND: Right panel showing tab settings
      Look for "Label" or "Name" input field
      May also show icon selector
```

```
COMMAND: CLICK
REF: {ref of label input}
ELEMENT: Tab label input
```

Select all existing text first:
```
COMMAND: PRESS_KEY
KEY: Meta+A (Mac) or Control+A (Windows)
```

```
COMMAND: TYPE
REF: {ref of label input}
ELEMENT: Tab label input
TEXT: {tabLabel}

RULES:
  - 1-2 words maximum
  - Clear and descriptive
  - Good: "Tasks", "Team", "Orders"
  - Bad: "All Tasks List", "Team Members"
```

### Step 7: Skip Tab Icon (Optional)

**⚠️ SKIP the emoji picker for tab icons.**

The emoji picker causes page state to exceed token limits. Leave the default icon.

The default icons are usually acceptable. If needed, icons can be changed manually later.

Proceed directly to Step 8 (verification).

### Step 8: Verify Tab Created

```
COMMAND: SNAPSHOT
PURPOSE: Confirm tab exists and is configured

VERIFY:
  - Tab appears in Navigation list in sidebar
  - Tab shows correct label
  - Tab shows correct icon
  - Preview shows collection from table
```

## Creating Tab in Menu Instead

If the screen should be in the Menu (not main Navigation):

In Step 3, click the "+" in the **Menu** section instead of Navigation.

The rest of the procedure is the same.

## Error Recovery

### Table Not in List

```
ISSUE: Target table doesn't appear in table selection

CAUSE: Table not linked to this app

FIX:
  1. Go to Data Editor (Data tab)
  2. Click team name in Data Sources
  3. Link the table to this app
  4. Return to Layout and retry
```

### Emoji Picker Issues

```
ISSUE: Emoji picker causes errors or page state too large

SOLUTION: Skip the emoji picker entirely.
          Leave default icons - they work fine.
          Users can change icons manually if needed.
```

### Preview Shows Empty Collection

```
ISSUE: Tab created but preview shows no items

CHECK:
  1. Does the table have any data?
  2. Is the data visible to current user?
  3. Are there row owner restrictions?

NOTE: If table is empty, this is expected
      Add sample data to see the collection
```

### Wrong Collection Style

```
ISSUE: Collection doesn't look right

NOTE: This procedure creates the tab with default collection style
      Collection style configuration is handled by configure-collection.md
      
REPORT: Tab created, collection style needs configuration
```

### Tab in Wrong Position

```
ISSUE: Tab appeared in Menu instead of Navigation (or vice versa)

FIX: Can drag tabs between Navigation and Menu
  1. Find the tab in sidebar
  2. Drag it to the correct section
  
Or: Delete and recreate in correct section
```

## Report Format

```
TAB CREATED:
  Label: {tabLabel}
  Icon: {icon/emoji}
  Source Table: {tableName}
  Position: Navigation | Menu
  Status: SUCCESS | FAILED
  Notes: {any issues, e.g., "needs collection style config"}
```
