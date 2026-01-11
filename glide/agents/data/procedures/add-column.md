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

```typescript
mcp__playwright__browser_navigate({
  url: `https://go.glideapps.com/app/${appId}/data`
})

// Expected Result:
// - Data Editor loads
// - "Data" tab is active
// - Table list visible in left sidebar
```

### Step 2: Select Target Table

```typescript
const sidebarSnapshot = mcp__playwright__browser_snapshot()
const tableRef = find(sidebarSnapshot, tableName)

mcp__playwright__browser_click({
  ref: tableRef,
  element: `${tableName} table in sidebar`
})

// Verification: Table should open
const tableSnapshot = mcp__playwright__browser_snapshot()
// Assert: Table header shows column names
// Assert: Table data is visible
```

### Step 3: Open Column Creation

**Method A: Keyboard Shortcut (Preferred)**

```typescript
mcp__playwright__browser_press_key({
  key: "Meta+Shift+Enter"  // Use Control+Shift+Enter on Windows
})

// Expected: Column creation panel appears on right side
// Verify by taking snapshot and checking for "Name" input and "Type" dropdown

const panelCheck = mcp__playwright__browser_snapshot()
if (!panelCheck.contains("Type")) {
  // Panel didn't appear, fall back to Method B
  useMethodB()
}
```

**Method B: UI Click (Fallback)**

```typescript
const columnSnapshot = mcp__playwright__browser_snapshot()
const columnHeaderRef = findFirst(columnSnapshot, "column header")

mcp__playwright__browser_click({
  ref: columnHeaderRef,
  element: "Column header"
})

// Wait for menu
wait(300)

const menuSnapshot = mcp__playwright__browser_snapshot()
const addColumnRef = find(menuSnapshot, "Add column right")

mcp__playwright__browser_click({
  ref: addColumnRef,
  element: "Add column right menu item"
})

// Verification: Column creation panel should appear
```

### Step 4: Enter Column Name

```typescript
const nameSnapshot = mcp__playwright__browser_snapshot()
const nameInputRef = find(nameSnapshot, "Name", "input")

mcp__playwright__browser_type({
  ref: nameInputRef,
  element: "Column name input",
  text: columnName
})

// Verification: Name should appear in input field
```

### Step 5: Select Column Type

```typescript
// Find and click type dropdown
const typeSnapshot = mcp__playwright__browser_snapshot()
const typeDropdownRef = find(typeSnapshot, "Type")

mcp__playwright__browser_click({
  ref: typeDropdownRef,
  element: "Column type dropdown"
})

// Wait for type picker to appear
wait(300)

// Search for column type
const searchSnapshot = mcp__playwright__browser_snapshot()
const searchInputRef = find(searchSnapshot, "search", "input")

mcp__playwright__browser_type({
  ref: searchInputRef,
  element: "Type search input",
  text: columnType
})

// Wait for filter to apply
wait(300)

// Click on matching type option
const typeOptionsSnapshot = mcp__playwright__browser_snapshot()
const typeOptionRef = find(typeOptionsSnapshot, columnType)

mcp__playwright__browser_click({
  ref: typeOptionRef,
  element: `${columnType} type option`
})

// Verification: Type picker should close and dropdown should show selected type
const verifySnapshot = mcp__playwright__browser_snapshot()
// Assert: Type dropdown shows columnType
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

```typescript
const saveSnapshot = mcp__playwright__browser_snapshot()
const saveButtonRef = find(saveSnapshot, "Save", "button")

mcp__playwright__browser_click({
  ref: saveButtonRef,
  element: "Save button"
})

// Wait for panel to close
wait(500)

// Verification: Panel should close and column should appear
const verifySnapshot = mcp__playwright__browser_snapshot()
// Assert: Column creation panel is closed
// Assert: New column appears in table header
```

### Step 8: Verify Column Created

```typescript
const finalSnapshot = mcp__playwright__browser_snapshot()

// Verify column exists
assert(finalSnapshot.contains(columnName))
// Verify column has correct type indicator

// Success criteria:
// ✅ Column "{columnName}" visible in table header
// ✅ Column shows correct type indicator (icon or label)
```

## Error Recovery Matrix

| Error | Detection | Recovery Strategy | Max Retries |
|-------|-----------|------------------|-------------|
| **Column panel doesn't appear** | Snapshot doesn't show "Name" input after keyboard shortcut | 1. Press Escape<br>2. Wait 500ms<br>3. Use Method B (UI click) instead | 1 |
| **Type not found in search** | No matching types in snapshot after typing search term | 1. Clear search (backspace)<br>2. Browse categories manually (Basic/Computed/AI)<br>3. Verify column type name is correct | 2 |
| **Save button disabled** | Button has disabled attribute in snapshot | 1. Check column name is not empty<br>2. Check type is selected<br>3. Look for validation error messages<br>4. Fill missing required fields | 1 |
| **Type dropdown won't open** | Snapshot unchanged after click | 1. Press Escape<br>2. Wait 500ms<br>3. Remove any tooltip overlays via evaluate<br>4. Retry click | 2 |
| **Table not found in sidebar** | Snapshot doesn't contain table name | 1. Wait 1s for sidebar to load<br>2. Scroll sidebar down<br>3. Check table is linked to app | 2 |
| **Column already exists** | Error message "Column name already exists" | 1. Use different column name<br>2. Or delete existing column first<br>3. Or update existing column instead | 0 |

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

Return structured output:

```json
{
  "status": "SUCCESS",
  "table": "Tasks",
  "column": "priority",
  "type": "Text",
  "timestamp": "2024-01-11T10:45:00Z",
  "notes": "Column added successfully"
}
```

**On failure:**
```json
{
  "status": "FAILED",
  "error": "Type dropdown didn't open after 2 retries",
  "step": "Step 5: Select Column Type",
  "table": "Tasks",
  "column": "priority",
  "recoveryAttempted": ["Pressed Escape", "Removed tooltips", "Retried click"],
  "suggestion": "Check for UI conflicts or try manual selection"
}
```
