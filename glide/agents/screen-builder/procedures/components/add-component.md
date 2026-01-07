---
name: add-component
description: Add a component to any screen in the Layout Editor
preconditions:
  - Screen exists
  - Must be on the correct screen (collection/detail/edit/add)
---

# Add Component Procedure

## Overview

Adds a component to a screen and configures its basic settings. Works for any component type.

## Important: Screen Navigation

You must be on the correct screen before adding components:

| Screen Type | How to Navigate |
|-------------|----------------|
| Collection | Select tab in Navigation |
| Detail | Click an item in the preview |
| Edit | Click Edit button on detail screen |
| Add | Click Add button on collection screen |

## Procedure

### Step 1: Navigate to Correct Screen

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/layout

EXPECTED: Layout Editor loads
```

**For Collection Screen:**
```
COMMAND: CLICK
REF: {ref of tab in Navigation}
ELEMENT: "{tabName}" tab

EXPECTED: Collection screen shown
```

**For Detail Screen:**
```
COMMAND: CLICK
REF: {ref of tab in Navigation}
ELEMENT: "{tabName}" tab
```

```
COMMAND: SNAPSHOT
PURPOSE: Find an item to click in preview
```

```
COMMAND: CLICK
REF: {ref of any item in collection preview}
ELEMENT: Any item in collection

EXPECTED: Preview navigates to detail screen
VERIFY: Left sidebar shows detail screen components
```

**For Edit Screen:**
(First navigate to detail screen, then:)
```
COMMAND: SNAPSHOT
PURPOSE: Find Edit button in preview
```

```
COMMAND: CLICK
REF: {ref of Edit button}
ELEMENT: Edit button in preview

EXPECTED: Edit form screen shown
```

**For Add Screen:**
(From collection screen:)
```
COMMAND: SNAPSHOT
PURPOSE: Find Add button in preview
```

```
COMMAND: CLICK
REF: {ref of Add button}
ELEMENT: Add/+ button in preview

EXPECTED: Add form screen shown
```

### Step 2: Open Component Picker

```
COMMAND: SNAPSHOT
PURPOSE: Find add component button

FIND: "+" button in Components section of left sidebar
      May say "Add component" or just show "+"
```

```
COMMAND: CLICK
REF: {ref of add component button}
ELEMENT: Add component button

EXPECTED: Component picker appears
VERIFY: Shows categories like Title, Collections, Text, etc.
```

### Step 3: Find the Component

```
COMMAND: SNAPSHOT
PURPOSE: Find search or component list

FIND: Search input at top of component picker
      Or browse categories
```

**Using Search:**
```
COMMAND: TYPE
REF: {ref of search input}
ELEMENT: Component search
TEXT: {componentName}

EXPECTED: Components filtered to matches
```

```
COMMAND: SNAPSHOT
PURPOSE: Find the component in results
```

**Or Browse Categories:**
```
COMMAND: CLICK
REF: {ref of category}
ELEMENT: "{categoryName}" category

EXPECTED: Shows components in that category
```

### Step 4: Add the Component

```
COMMAND: CLICK
REF: {ref of component}
ELEMENT: "{componentName}" component

EXPECTED: 
  - Component added to screen
  - Component appears in left sidebar component list
  - Component shows in preview
  - Right panel shows component configuration
```

### Step 5: Configure the Component

Configuration varies by component type. Common patterns:

#### Bind to Data Column

```
COMMAND: SNAPSHOT
PURPOSE: Find data binding options

FIND: Dropdown showing column options or "Data" field
```

```
COMMAND: CLICK
REF: {ref of data dropdown}
ELEMENT: Data/Column selector
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of target column}
ELEMENT: "{columnName}" column

EXPECTED: Component bound to column
```

#### Set Label/Title

```
COMMAND: SNAPSHOT
PURPOSE: Find label input

FIND: "Label" or "Title" input field
```

```
COMMAND: TYPE
REF: {ref of label input}
ELEMENT: Label input
TEXT: {labelText}
```

#### Configure Style Options

```
COMMAND: SNAPSHOT
PURPOSE: Find style options

FIND: Style toggles, dropdowns, or radio buttons
      May be in "Options" tab of right panel
```

```
COMMAND: CLICK
REF: {ref of style option}
ELEMENT: "{optionName}" option

EXPECTED: Style applied
```

### Step 6: Position the Component (Optional)

Components can be reordered in the left sidebar:

```
COMMAND: DRAG
FROM_REF: {ref of component in sidebar}
FROM_ELEMENT: "{componentName}" component
TO_REF: {ref of target position}
TO_ELEMENT: Position after "{otherComponent}"

EXPECTED: Component moves to new position
```

### Step 7: Verify Component Added

```
COMMAND: SNAPSHOT
PURPOSE: Confirm component exists and is configured

VERIFY:
  - Component appears in left sidebar
  - Component shows in preview
  - Data binding shows correct data
```

## Component-Specific Configuration

### Title Components (Profile, Cover, Image)

```
Configure:
- Image: Column with image URL
- Title: Main text (name, title)
- Subtitle: Secondary text (role, description)
```

### Big Numbers

```
Configure:
- Data: Column with numeric value
- Label: What the number represents
- Format: Number format options
```

### Contact

```
Configure:
- Email: Column with email address
- Phone: Column with phone number
(Creates tap-to-call/email buttons)
```

### Inline Collection

```
Configure:
- Data: Relation column (must be a relation!)
- Style: Card, List, Table, etc.
- Title/fields: What to show per item
```

### Progress

```
Configure:
- Data: Number column (0-100 or 0-1)
- May need to set min/max values
```

### Container

```
Configure:
- Columns: Number of columns (1, 2, 3, 4)
- Add components INSIDE the container
```

## Error Recovery

### Component Not Found

```
ISSUE: Component doesn't appear in picker

CHECK:
  1. Correct spelling?
  2. Try category browsing
  3. Some components are Beta/Business only

ALTERNATIVES:
  "Big Numbers" → search "big"
  "Rich Text" → search "rich"
  "Inline Collection" → search "inline" or "collection"
```

### Can't Add Component

```
ISSUE: Component won't add to screen

CHECK:
  1. Are you on the right screen type?
  2. Some components only work on certain screens
  3. Is there a parent container selected?
```

### Data Binding Empty

```
ISSUE: No columns show in data selector

CHECK:
  1. Is this screen backed by a table?
  2. Does the table have columns?
  3. For Inline Collection: need a Relation column
```

### Component in Wrong Position

```
FIX: Drag component in sidebar to reorder
     Or delete and re-add in correct position
```

## Deleting Components

To remove a component:

```
COMMAND: SNAPSHOT
PURPOSE: Find component to delete

FIND: Component in left sidebar
```

```
COMMAND: CLICK
REF: {ref of component}
ELEMENT: "{componentName}" component

EXPECTED: Component selected
```

```
COMMAND: PRESS_KEY
KEY: Backspace (or Delete)

EXPECTED: Component removed

ALTERNATIVE: Right-click → Delete
```

## Report Format

```
COMPONENT ADDED:
  Screen: {collection | detail | edit | add} on {tabName}
  Component: {componentName}
  Data Binding: {column or relation bound to}
  Configuration: {key settings}
  Status: SUCCESS | FAILED
```
