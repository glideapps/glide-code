---
name: math-column
description: Add a Math computed column for calculations
preconditions:
  - Table exists with numeric source columns
  - Must be in Data Editor
---

# Math Column Procedure

Creates calculations using arithmetic operations on numeric columns.

## Use Cases

- Calculate totals: `Price * Quantity`
- Compute percentages: `Completed / Total * 100`
- Apply formulas: `(Subtotal * TaxRate) + Subtotal`
- Calculate differences: `Revenue - Expenses`

## Quick Reference

| Operator | Meaning | Example |
|----------|---------|---------|
| `+` | Add | `Price + Tax` |
| `-` | Subtract | `Total - Discount` |
| `*` | Multiply | `Price * Quantity` |
| `/` | Divide | `Sum / Count` |
| `( )` | Group | `(A + B) * C` |

## Functions
LOG, ABS, MIN, MAX, FLOOR, CEILING, ROUND, TRUNC, ASIN, COS, ACOS, ATAN, MOD, SECOND, MINUTE, HOUR, DAY, MONTH, WEEKDAY, WEEKNUM, YEAR

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
FIND: "+" button or "Add column" in table header
```

```
COMMAND: CLICK
REF: {ref of add column button}
ELEMENT: Add column button (+ icon in header)

EXPECTED: Column creation panel opens
```

### Step 3: Enter Column Name

```
COMMAND: SNAPSHOT
PURPOSE: Find name input field
```

```
COMMAND: TYPE
REF: {ref of name input}
ELEMENT: Column name input
TEXT: {columnName}

EXAMPLE: "Total", "Subtotal", "Profit Margin"
```

### Step 4: Select Math Type

```
COMMAND: CLICK
REF: {ref of type dropdown}
ELEMENT: Type dropdown (shows "Text" or current type)
```

```
COMMAND: SNAPSHOT
PURPOSE: Find computed columns section
```

```
COMMAND: CLICK
REF: {ref of Computed Columns section}
ELEMENT: "Computed Columns" category
```

```
COMMAND: SNAPSHOT
PURPOSE: Find Math Column option
```

```
COMMAND: CLICK
REF: {ref of Math option}
ELEMENT: "Math Column" option

EXPECTED: Math formula editor appears
```

### Step 5: Build Formula

```
COMMAND: SNAPSHOT
PURPOSE: Find formula input area
FIND: 
  - Text area for entering formula
  - Column names displayed for reference/insertion
  - Operators toolbar (may exist)
```

**Option A: Type formula directly**

```
COMMAND: CLICK
REF: {ref of formula input}
ELEMENT: Formula input area
```

```
COMMAND: TYPE
REF: {ref of formula input}
ELEMENT: Formula input
TEXT: {formula}

EXAMPLES:
  Price * Quantity
  (Subtotal + Tax) * (1 - Discount)
  Revenue - Expenses
  Score / MaxScore * 100

NOTE: Use exact column names as they appear in table
```

**Option B: Use column picker (more reliable)**

```
COMMAND: SNAPSHOT
PURPOSE: Find column insertion method
FIND: 
  - Column chips that can be clicked to insert. 3 dots stacked atop of each other.
  - select the column to be used from the dropdown.
```

For each column in formula:

```
COMMAND: CLICK
REF: {ref of column chip or insert button}
ELEMENT: "{columnName}" column reference

EXPECTED: Column name inserted into formula
```

Then type operators (`+`, `-`, `*`, `/`) between columns.

### Step 6: Verify Formula Preview

```
COMMAND: SNAPSHOT
PURPOSE: Check formula preview

FIND: Preview section showing calculated results

VERIFY:
  - No red error messages
  - Preview shows expected numbers
  - No "undefined", "NaN", or "#ERROR" results
  - Values look reasonable for the data
```

### Step 7: Save Column

```
COMMAND: CLICK
REF: {ref of save/done button}
ELEMENT: Save button or Done button

EXPECTED: Math column added to table with calculated values
```

---

## Common Formulas

### Financial

| Purpose | Formula |
|---------|---------|
| Line Total | `Price * Quantity` |
| With Tax | `Subtotal * ROUND(1 + TaxRate)` |
| Discount | `Price * (1 - DiscountPercent)` |
| Profit | `Revenue - Cost` |
| Margin % | `(Revenue - Cost) / Revenue * 100` |

### Metrics

| Purpose | Formula |
|---------|---------|
| Percentage | `Value / Total * 100` |
| Average (manual) | `(A + B + C) / 3` |
| Difference | `CurrentValue - PreviousValue` |
| Weighted | `(Score1 * Weight1 + Score2 * Weight2) / (Weight1 + Weight2)` |

---

## Error Recovery

### "Invalid formula" Error

```
ISSUE: Red error, formula rejected

CHECK:
  - Column names spelled exactly as they appear?
  - All columns exist in THIS table?
  - Columns contain numeric data?
  - Parentheses balanced?

FIX: Use column picker instead of typing names
```

### Preview Shows NaN or Undefined

```
ISSUE: Calculated values show NaN or undefined

CAUSE: Usually empty/null values in source columns

FIX OPTIONS:
  - Ensure source columns have data
  - Use If-Then-Else to handle empty cases first
  - Check that source columns are numeric type
```

### Division Results Wrong

```
ISSUE: Division gives unexpected results

CHECK:
  - Dividing by zero anywhere?
  - Order of operations correct?
  - Need parentheses?

EXAMPLE:
  Wrong: A + B / C = A + (B/C)
  Right: (A + B) / C
```

---

## Tips

1. **Use parentheses liberally** - Makes order of operations explicit
2. **Check source column types** - Math only works on numbers
3. **Handle empty values** - Create If-Then-Else first if nulls exist
4. **Test with sample data** - Verify preview before saving
5. **Name columns clearly** - "Total Price" not "Col1"

---

## Report Format

```
MATH COLUMN ADDED:
  Table: {tableName}
  Column: {columnName}
  Formula: {the expression}
  Status: SUCCESS | FAILED
  Sample Output: {example calculated value}
```
