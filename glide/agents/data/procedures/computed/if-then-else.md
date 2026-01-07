---
name: if-then-else-column
description: Add conditional logic column with If-Then-Else
preconditions:
  - Table exists with column to evaluate
  - Must be in Data Editor
---

# If → Then → Else Column Procedure

Creates conditional values based on logic rules. Essential for status indicators, categorization, and dynamic text.

## Use Cases

- Status emojis: If Complete → "✅" else "⏳"
- Priority colors: If Priority = "High" → "🔴" else "🟢"  
- Categories: If Amount > 1000 → "Large" else "Small"
- Conditional text: If Overdue → "⚠️ Past Due" else "On Track"

## Quick Reference - Operators

| Operator | Use For | Example |
|----------|---------|---------|
| `is` / `=` | Exact match | Status = "Complete" |
| `is not` / `!=` | Not equal | Status != "Cancelled" |
| `contains` | Partial text | Name contains "Corp" |
| `is empty` | Null check | Notes is empty |
| `is not empty` | Has value | Email is not empty |
| `>` | Greater than | Amount > 1000 |
| `<` | Less than | DaysLeft < 7 |
| `>=` | Greater or equal | Score >= 80 |
| `<=` | Less or equal | Inventory <= 10 |

---

## Procedure

### Step 1: Navigate to Table

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/data
```

```
COMMAND: SNAPSHOT
PURPOSE: Find and select the target table
```

```
COMMAND: CLICK
REF: {ref of table in sidebar}
ELEMENT: "{tableName}" in table list
```

### Step 2: Open Add Column

```
COMMAND: SNAPSHOT
PURPOSE: Find add column button
```

```
COMMAND: CLICK
REF: {ref of add column button}
ELEMENT: Add column button (+ icon in header)

EXPECTED: Column creation panel opens
```

### Step 3: Enter Column Name

```
COMMAND: TYPE
REF: {ref of name input}
ELEMENT: Column name input
TEXT: {columnName}

EXAMPLES: "Status Icon", "Priority Badge", "Size Category"
```

### Step 4: Select If-Then-Else Type

```
COMMAND: CLICK
REF: {ref of type dropdown}
ELEMENT: Type dropdown
```

```
COMMAND: SNAPSHOT
PURPOSE: Find computed columns
```

```
COMMAND: CLICK
REF: {ref of Computed Columns section}
ELEMENT: "Computed Columns" category
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of If-Then-Else option}
ELEMENT: "If → Then → Else Column" option

EXPECTED: Condition builder UI appears
```

### Step 5: Configure IF Condition

The condition builder has three parts:
1. **Left side**: Column to check
2. **Middle**: Operator (=, >, contains, etc.)
3. **Right side**: Value to compare against

```
COMMAND: SNAPSHOT
PURPOSE: Identify condition builder elements

FIND:
  - Column selector dropdown (left)
  - Operator dropdown (middle)
  - Value input (right)
  - "Then" value input
```

**Select the column to evaluate:**

```
COMMAND: CLICK
REF: {ref of left dropdown}
ELEMENT: Column selector (IF condition)
```

```
COMMAND: SNAPSHOT
PURPOSE: Find column in list
```

```
COMMAND: CLICK
REF: {ref of target column}
ELEMENT: "{columnName}" in column list
```

**Select the operator:**

```
COMMAND: CLICK
REF: {ref of operator dropdown}
ELEMENT: Operator dropdown
```

```
COMMAND: SNAPSHOT
PURPOSE: Find desired operator
```

```
COMMAND: CLICK
REF: {ref of operator}
ELEMENT: "{operator}" option

COMMON OPERATORS:
  - "is" for exact match
  - "is not" for exclusion
  - "contains" for partial match
  - "is empty" / "is not empty" for null checks
  - ">" "<" ">=" "<=" for numeric comparisons
```

**Enter the comparison value:**

```
COMMAND: CLICK
REF: {ref of value input}
ELEMENT: Comparison value input
```

```
COMMAND: TYPE
REF: {ref of value input}
ELEMENT: Comparison value
TEXT: {value}

EXAMPLES:
  "Complete" (text match)
  "High" (priority check)
  1000 (numeric threshold)
  
NOTE: For "is empty" / "is not empty", skip this step
```

### Step 6: Set THEN Value

```
COMMAND: SNAPSHOT
PURPOSE: Find "Then" input
```

```
COMMAND: CLICK
REF: {ref of then input}
ELEMENT: "Then" value input
```

```
COMMAND: TYPE
REF: {ref of then input}
ELEMENT: Then value
TEXT: {resultIfTrue}

EXAMPLES:
  "✅ Done"
  "🔴"
  "Large Order"
  "High Priority"
```

### Step 7: Set ELSE Value (or Add More Conditions)

```
COMMAND: SNAPSHOT
PURPOSE: Find Else section

FIND:
  - "Else" value input (for simple two-way)
  - "Add condition" button (for multiple conditions)
```

**For simple IF-ELSE (two outcomes):**

```
COMMAND: CLICK
REF: {ref of else input}
ELEMENT: "Else" value input
```

```
COMMAND: TYPE
REF: {ref of else input}
ELEMENT: Else value
TEXT: {resultIfFalse}

EXAMPLES:
  "⏳ Pending"
  "🟢"
  "Small Order"
```

**For multiple conditions (ELSE IF):**

```
COMMAND: CLICK
REF: {ref of add condition button}
ELEMENT: "Add condition" or "+" button

EXPECTED: New condition row appears
```

Repeat Steps 5-6 for each additional condition, then set final Else.

### Step 8: Verify Preview

```
COMMAND: SNAPSHOT
PURPOSE: Check preview values

VERIFY:
  - Conditions evaluate correctly
  - Expected values showing for sample rows
  - No errors or empty results
```

### Step 9: Save Column

```
COMMAND: CLICK
REF: {ref of save button}
ELEMENT: Save button

EXPECTED: If-Then-Else column added with conditional values
```

---

## Common Patterns

### Status Emoji Column

```
IF Status = "Complete" THEN "✅ Done"
ELSE IF Status = "In Progress" THEN "🔄 Working"
ELSE IF Status = "Blocked" THEN "🚫 Blocked"
ELSE "⏳ Not Started"
```

### Priority Badge

```
IF Priority = "High" THEN "🔴 High"
ELSE IF Priority = "Medium" THEN "🟡 Medium"
ELSE "🟢 Low"
```

### Amount Category

```
IF Amount > 10000 THEN "Enterprise"
ELSE IF Amount > 1000 THEN "Business"
ELSE IF Amount > 100 THEN "Professional"
ELSE "Starter"
```

### Overdue Check

```
IF DueDate < Today THEN "⚠️ Overdue"
ELSE IF DueDate = Today THEN "📅 Due Today"
ELSE "✓ On Track"
```

### Empty Value Handler

```
IF Notes is empty THEN "(No notes)"
ELSE Notes
```

### Boolean Display

```
IF Active = true THEN "✅ Active"
ELSE "❌ Inactive"
```

---

## Error Recovery

### Condition Not Matching

```
ISSUE: All rows showing Else value when they shouldn't

CHECK:
  - Is the comparison value spelled exactly right?
  - Is there whitespace in the data? (leading/trailing spaces)
  - Is the column type correct? (text vs number)
  - Case sensitivity? ("High" vs "high")

FIX: 
  - Use "contains" instead of "is" for partial matching
  - Check actual data values in the table
```

### Can't Find Column

```
ISSUE: Column not appearing in dropdown

CHECK:
  - Is it a column in THIS table?
  - Is it a computed column that hasn't saved yet?
  - Is the panel scrollable?

FIX: Save any pending columns first, refresh page
```

### Numeric Comparison Failing

```
ISSUE: > or < not working as expected

CHECK:
  - Is the column actually numeric type?
  - Are there text values mixed in?
  - Is the comparison value a number (not "1000" in quotes)?

FIX: Ensure column type is Number, not Text
```

---

## Tips

1. **Order matters** - Put most specific conditions first
2. **Always have an Else** - Handles unexpected values
3. **Use emojis** - Visual indicators are powerful in Glide UIs
4. **Test edge cases** - What happens with empty/null values?
5. **Keep it simple** - More than 4-5 conditions? Consider a different approach

---

## Report Format

```
IF-THEN-ELSE COLUMN ADDED:
  Table: {tableName}
  Column: {columnName}
  Logic:
    IF {condition1} THEN "{value1}"
    ELSE IF {condition2} THEN "{value2}"
    ELSE "{defaultValue}"
  Status: SUCCESS | FAILED
  Sample Output: {example values from preview}
```
