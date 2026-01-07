---
name: add-column
description: Add a basic column to an existing table in the Data Editor
preconditions:
  - Browser is authenticated to Glide
  - App ID is known
---

# Add Basic Column Procedure

## Overview

This procedure adds a **basic data column** to an existing table. For computed columns, see the `computed/` folder.

## For Computed Columns

| Column Type | Procedure |
|-------------|-----------|
| Math | `computed/math.md` |
| If → Then → Else | `computed/if-then-else.md` |
| Template | `computed/template.md` |
| Lookup | `computed/lookup.md` |
| Rollup | `computed/rollup.md` |
| Relation | `add-relation.md` |

## Basic Column Types

| Type | Data Format | Example Values |
|------|-------------|----------------|
| Text | `string` | `"Hello world"`, `"Product A"` |
| Number | `number` | `42`, `3.14`, `-100` |
| Boolean | `boolean` | `true`, `false`, `null` |
| Image | `uri` | `"https://example.com/photo.jpg"` |
| Date & Time | `dateTime` | `"2024-07-29T14:04:15.561Z"` |
| URL | `uri` | `"https://example.com"` |
| Row ID | `string` | Auto-generated unique ID |
| Rich Text | `string` | Markdown/HTML formatted text |
| Email | `string` | `"user@example.com"` |
| Phone Number | `string` | `"+1-555-123-4567"` |
| Duration | `number` | Milliseconds or formatted duration |
| Emoji | `string` | `"🎉"`, `"✅"` |
| Multiple Files | `array<uri>` | Array of file URLs |
| Multiple Images | `array<uri>` | Array of image URLs |

## Preconditions

- Must have app ID
- Must know target table name
- Must know column name and type to add

## Procedure

### Step 1: Navigate to Data Editor

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/data

EXPECTED: Data Editor loads
VERIFY: Page shows "Data" tab as active, table list visible in left sidebar
```

### Step 2: Select Target Table

```
COMMAND: SNAPSHOT
PURPOSE: Find the target table in sidebar

FIND: Element containing table name "{tableName}" in left sidebar
NOTE: Tables are listed under "Data Sources" section
```

```
COMMAND: CLICK
REF: {ref of table name element}
ELEMENT: "{tableName}" table in sidebar

EXPECTED: Table opens, showing columns and data in main area
VERIFY: Table header shows column names
```

### Step 3: Open Column Creation

**Method A: Keyboard Shortcut (Preferred)**

```
COMMAND: PRESS_KEY
KEY: Meta+Shift+Enter (Mac) or Control+Shift+Enter (Windows)

EXPECTED: Column creation panel appears on right side
VERIFY: Panel shows "Name" input field and "Type" dropdown
```

**Method B: UI Click (Fallback)**

```
COMMAND: SNAPSHOT
PURPOSE: Find column header to click

FIND: Any column header in the table
```

```
COMMAND: CLICK
REF: {ref of any column header}
ELEMENT: Column header

EXPECTED: Column menu appears
```

```
COMMAND: SNAPSHOT
PURPOSE: Find "Add column right" option
```

```
COMMAND: CLICK
REF: {ref of "Add column right" option}
ELEMENT: "Add column right" menu item

EXPECTED: Column creation panel appears
```

### Step 4: Enter Column Name

```
COMMAND: SNAPSHOT
PURPOSE: Find the name input field

FIND: Input field with placeholder "Column name" or "Name"
      Usually in the right panel that just appeared
```

```
COMMAND: TYPE
REF: {ref of name input}
ELEMENT: Column name input
TEXT: {columnName}

EXPECTED: Name appears in input field
```

### Step 5: Select Column Type

```
COMMAND: SNAPSHOT
PURPOSE: Find the type dropdown

FIND: Button or dropdown showing "Type" or current type name (often "Text" by default)
      Located below the name input in the column config panel
```

```
COMMAND: CLICK
REF: {ref of type dropdown}
ELEMENT: Column type dropdown

EXPECTED: Type picker modal/dropdown appears
VERIFY: Shows categories like "Basic", "Computed", "AI", "Integrations"
        Has a search input at top
```

```
COMMAND: SNAPSHOT
PURPOSE: Find search input in type picker
```

```
COMMAND: TYPE
REF: {ref of type search input}
ELEMENT: Type search input
TEXT: {columnType}

EXPECTED: Type list filters to show matching types
WAIT: 300ms for filter to apply
```

```
COMMAND: SNAPSHOT
PURPOSE: Find the matching type option
```

```
COMMAND: CLICK
REF: {ref of matching type}
ELEMENT: "{columnType}" type option

EXPECTED: Type picker closes
VERIFY: Type dropdown now shows selected type name
```

### Step 6: Configure Type-Specific Options (Basic Types)

**Most basic types require no additional configuration:**
- Text, Number, Boolean, Date & Time, URL, Row ID → Skip to Step 7
- Image, Email, Phone, Emoji → Skip to Step 7
- Rich Text → Skip to Step 7
- Multiple Files/Images → Skip to Step 7
- Duration → May have format options, usually defaults are fine

**For Computed Columns:**
Use the dedicated procedures instead of this one:
- **Relation** → `add-relation.md`
- **Math** → `computed/math.md`
- **If-Then-Else** → `computed/if-then-else.md`
- **Template** → `computed/template.md`
- **Lookup** → `computed/lookup.md`
- **Rollup** → `computed/rollup.md`

### Step 7: Save Column

```
COMMAND: SNAPSHOT
PURPOSE: Find save button

FIND: "Save" or "Done" button in the column config panel
      Usually at bottom of the right panel
```

```
COMMAND: CLICK
REF: {ref of save button}
ELEMENT: Save button

EXPECTED: Panel closes
VERIFY: New column appears in table header
```

### Step 8: Verify Column Created

```
COMMAND: SNAPSHOT
PURPOSE: Confirm column exists

VERIFY: Column "{columnName}" visible in table header
        Column shows correct type indicator (icon or label)
```

## Error Recovery

### Column Panel Doesn't Appear (after keyboard shortcut)

```
COMMAND: PRESS_KEY
KEY: Escape

COMMAND: SNAPSHOT
PURPOSE: Check current state

THEN: Try Method B (UI click) instead
```

### Type Not Found in Search

```
COMMAND: SNAPSHOT
PURPOSE: See what types are available

CHECK: Is the type under a different category?
       - Basic types: Text, Number, Boolean, Date, Image, URL, etc.
       - Computed types: Relation, Math, If-Then-Else, Template, Lookup, Rollup
       - AI types: Generate Text, Image to Text, Text to Choice

TRY: Clear search and browse categories manually

NOTE: For computed columns, use the dedicated procedures in computed/
```

### Save Button Disabled

```
COMMAND: SNAPSHOT
PURPOSE: Find validation error

CHECK: 
  - Is column name empty?
  - Is there a red error message below name field?
  - Is type selection complete?

FIX: Fill in required fields
```

### Type Dropdown Won't Open

```
COMMAND: PRESS_KEY
KEY: Escape

COMMAND: WAIT
SECONDS: 0.5

COMMAND: SNAPSHOT
PURPOSE: Check for blocking elements

CHECK: Is there a tooltip or overlay blocking?

IF overlay found:
  COMMAND: EVALUATE
  CODE: () => { document.querySelectorAll('[class*="tooltip"]').forEach(el => el.remove()); return true; }

THEN: Retry clicking type dropdown
```

## Basic Column Type Reference

| Type Name | Search Term | Data Format |
|-----------|-------------|-------------|
| Text | "text" | `string` |
| Number | "number" | `number` |
| Boolean | "boolean" | `boolean` |
| Date & Time | "date" | `dateTime` |
| Image | "image" | `uri` |
| URL | "url" | `uri` |
| Row ID | "row id" | `string` |
| Rich Text | "rich" | `string` |
| Email | "email" | `string` |
| Phone Number | "phone" | `string` |
| Duration | "duration" | `number` |
| Emoji | "emoji" | `string` |
| Multiple Files | "multiple files" | `array<uri>` |
| Multiple Images | "multiple images" | `array<uri>` |

## Computed Columns (Use Separate Procedures)

| Type | Search Term | Procedure |
|------|-------------|-----------|
| Relation | "relation" | `add-relation.md` |
| Math | "math" | `computed/math.md` |
| If-Then-Else | "if" | `computed/if-then-else.md` |
| Template | "template" | `computed/template.md` |
| Lookup | "lookup" | `computed/lookup.md` |
| Rollup | "rollup" | `computed/rollup.md` |

## Report Format

After completing:

```
COLUMN ADDED:
  Table: {tableName}
  Column: {columnName}
  Type: {columnType}
  Status: SUCCESS | FAILED
  Notes: {any issues or special config}
```
