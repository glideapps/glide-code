---
name: configure-collection
description: Configure a collection's style and displayed columns
preconditions:
  - Tab exists with collection
  - Must navigate to collection screen
---

# Configure Collection Procedure

## Overview

Changes a collection's style (Card, List, Table, etc.) and configures which columns/fields are displayed.

## Collection Styles

| Style | Best For |
|-------|----------|
| Card | Visual items, images, browsing |
| List | Simple scanning, quick selection |
| Table | Comparing data, numbers, many columns |
| Data Grid | Inline editing |
| Checklist | Tasks with checkboxes |
| Calendar | Date-based items |
| Kanban | Status workflows |

## Procedure

### Step 1: Navigate to Layout Editor

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/layout

EXPECTED: Layout Editor loads
```

### Step 2: Select the Tab

```
COMMAND: SNAPSHOT
PURPOSE: Find target tab in Navigation section

FIND: "{tabName}" in left sidebar under Navigation or Menu
```

```
COMMAND: CLICK
REF: {ref of tab}
ELEMENT: "{tabName}" tab

EXPECTED: Tab selected, collection screen shown in preview
VERIFY: Left sidebar shows Components for this screen
```

### Step 3: Select the Collection Component

```
COMMAND: SNAPSHOT
PURPOSE: Find the collection component

FIND: In left sidebar Components list:
      - Component named "Collection" or the table name
      - Or click directly on the collection in preview
```

```
COMMAND: CLICK
REF: {ref of collection component}
ELEMENT: Collection component

EXPECTED: Collection selected
VERIFY: Right panel shows collection configuration options
```

### Step 4: Change Collection Style

```
COMMAND: SNAPSHOT
PURPOSE: Find style selector

FIND: "Style" dropdown or style options
      May show current style (e.g., "Card", "List")
      Usually near top of right panel
```

```
COMMAND: CLICK
REF: {ref of style dropdown}
ELEMENT: Collection style selector

EXPECTED: Style options appear
```

```
COMMAND: SNAPSHOT
PURPOSE: Find desired style option
```

```
COMMAND: CLICK
REF: {ref of desired style}
ELEMENT: "{styleName}" style option

EXPECTED: Collection style changes
VERIFY: Preview updates to show new style
```

### Step 5: Configure Displayed Fields/Columns

The configuration depends on collection style:

#### For Card Style:

```
COMMAND: SNAPSHOT
PURPOSE: Find card configuration options

FIND: Options like:
      - "Title" - main text field
      - "Subtitle" - secondary text
      - "Image" - image column
      - "Details" - additional info
```

For each field:
```
COMMAND: CLICK
REF: {ref of field dropdown}
ELEMENT: "{fieldName}" field selector
```

```
COMMAND: SNAPSHOT
PURPOSE: Find column options
```

```
COMMAND: CLICK
REF: {ref of desired column}
ELEMENT: "{columnName}" column

EXPECTED: Field bound to selected column
```

#### For List Style:

```
COMMAND: SNAPSHOT
PURPOSE: Find list configuration

FIND: Options like:
      - "Title" - main text
      - "Subtitle" - secondary text  
      - "Image" - thumbnail
```

Configure as above for Card.

#### For Table Style:

```
COMMAND: SNAPSHOT
PURPOSE: Find column configuration

FIND: "Columns" section
      - List of visible columns
      - Option to add/remove columns
      - Column order controls
```

**To add a column:**
```
COMMAND: CLICK
REF: {ref of "Add column" button}
ELEMENT: Add column button

EXPECTED: Column picker appears
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of column to add}
ELEMENT: "{columnName}" column

EXPECTED: Column added to table
```

**To remove a column:**
```
COMMAND: SNAPSHOT
PURPOSE: Find column to remove

FIND: Column in the list with "x" or remove button
```

```
COMMAND: CLICK
REF: {ref of remove button for column}
ELEMENT: Remove "{columnName}" column

EXPECTED: Column removed from table
```

**To reorder columns:**
```
COMMAND: DRAG
FROM_REF: {ref of column to move}
FROM_ELEMENT: "{columnName}" column
TO_REF: {ref of target position}
TO_ELEMENT: Position after "{otherColumn}"

EXPECTED: Column order changed
```

### Step 6: Configure Additional Options

```
COMMAND: SNAPSHOT
PURPOSE: Find additional options

LOOK FOR:
  - "Show search" toggle
  - "Show filter" toggle
  - "Show add button" toggle
  - Row action settings
  - Sorting options
```

Configure as needed:
```
COMMAND: CLICK
REF: {ref of option toggle}
ELEMENT: "{optionName}" toggle

EXPECTED: Option enabled/disabled
```

### Step 7: Verify Configuration

```
COMMAND: SNAPSHOT
PURPOSE: Confirm collection is configured correctly

VERIFY:
  - Collection shows correct style in preview
  - Configured columns/fields are visible
  - Data displays correctly
  - On desktop view (switch if needed)
```

## Switching to Desktop View

For better review of Table collections:

```
COMMAND: SNAPSHOT
PURPOSE: Find device switcher

FIND: Device icons in preview toolbar (phone/tablet/desktop)
```

```
COMMAND: CLICK
REF: {ref of desktop icon}
ELEMENT: Desktop preview button

EXPECTED: Preview shows desktop-width layout
```

## Error Recovery

### Style Dropdown Won't Open

```
TRY:
  1. Make sure collection component is selected
  2. Press Escape and reselect collection
  3. Look for style options in different location
```

### Column Not Available

```
ISSUE: Column doesn't appear in selection

CHECK:
  1. Does column exist in the source table?
  2. Is it a computed column that hasn't saved?
  3. Is it in the correct table?

FIX: Go to Data Editor and verify column exists
```

### Preview Not Updating

```
TRY:
  1. Click elsewhere then back on collection
  2. Refresh the page
  3. Check if changes saved
```

### Table Columns Cut Off

```
ISSUE: Table columns don't all fit

CHECK: Are you in Desktop view?
       Mobile preview will truncate columns

TRY:
  1. Switch to Desktop preview
  2. Remove less important columns
  3. Use column width settings if available
```

## Style-Specific Tips

### Card
- Always bind an Image field if data has images
- Title should be the main identifier (name, title)
- Keep subtitle short - it may truncate

### List  
- Best for simple 2-3 field items
- Consider adding image for visual interest
- Good for quick selection/navigation

### Table
- Best on desktop - hard to use on mobile
- Show 4-6 columns maximum
- Put most important columns first
- Consider adding status emoji column

### Kanban
- Requires a column to group by (status, category)
- Configure the "Group by" field
- Each group becomes a Kanban column

### Calendar
- Requires a date column
- Configure which date field to use
- Items show on their date

## Report Format

```
COLLECTION CONFIGURED:
  Tab: {tabName}
  Style: {Card | List | Table | ...}
  Fields/Columns: {list of visible fields}
  Options: {enabled options like search, filter}
  Status: SUCCESS | FAILED
  Notes: {any issues}
```
