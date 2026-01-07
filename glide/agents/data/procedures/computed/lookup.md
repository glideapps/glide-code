---
name: lookup-column
description: Add a Lookup column to pull values through a relation
preconditions:
  - Table has an existing Relation column to another table
  - Must be in Data Editor
---

# Lookup Column Procedure

Pulls a specific column value from a related table through an existing relation. The relation must exist first.

## Use Cases

- Get customer name on an Orders table (via Customer relation)
- Pull product price onto OrderItems (via Product relation)
- Show project name on Tasks (via Project relation)
- Display category name on Products (via Category relation)

## Prerequisites

⚠️ **You must have a Relation column first!**

Lookup columns work BY pulling data THROUGH a relation. If you don't have a relation, create one first using `add-relation.md`.

```
[Orders] ──relation──> [Customers]
    │
    └── Lookup: CustomerName (pulls Name from Customers)
```

---

## Procedure

### Step 1: Navigate to Table

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/data
```

```
COMMAND: SNAPSHOT
PURPOSE: Find and select the table that NEEDS the lookup (not the source table)
```

```
COMMAND: CLICK
REF: {ref of table in sidebar}
ELEMENT: "{tableName}" in table list

EXAMPLE: Click "Orders" if you want to show customer info on orders
```

### Step 2: Verify Relation Exists

```
COMMAND: SNAPSHOT
PURPOSE: Confirm a relation column exists

FIND: A column with a relation icon (link symbol) or 
      Column that shows related table name

IF NOT FOUND: 
  STOP - Must create relation first using add-relation.md
```

### Step 3: Open Add Column

```
COMMAND: CLICK
REF: {ref of add column button}
ELEMENT: Add column button (+ icon in header)

EXPECTED: Column creation panel opens
```

### Step 4: Enter Column Name

```
COMMAND: TYPE
REF: {ref of name input}
ELEMENT: Column name input
TEXT: {columnName}

EXAMPLES: 
  "Customer Name" (if looking up from Customers)
  "Product Price" (if looking up from Products)
  "Project Title" (if looking up from Projects)
  
TIP: Name it what the value IS, not "Lookup of X"
```

### Step 5: Select Lookup Type

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
REF: {ref of Lookup option}
ELEMENT: "Lookup Column" option

EXPECTED: Lookup configuration panel appears
```

### Step 6: Select the Relation

```
COMMAND: SNAPSHOT
PURPOSE: Find relation selector

FIND: Dropdown to select which relation to look through
```

```
COMMAND: CLICK
REF: {ref of relation dropdown}
ELEMENT: Relation selector
```

```
COMMAND: SNAPSHOT
PURPOSE: Find the correct relation in list
```

```
COMMAND: CLICK
REF: {ref of target relation}
ELEMENT: "{relationColumnName}" relation

EXAMPLE: Select "Customer" relation to look up customer data
```

### Step 7: Select Column to Look Up

```
COMMAND: SNAPSHOT
PURPOSE: Find column selector

FIND: Dropdown showing columns from the RELATED table
```

```
COMMAND: CLICK
REF: {ref of column dropdown}
ELEMENT: Column to lookup selector
```

```
COMMAND: SNAPSHOT
PURPOSE: Find the column you want to pull
```

```
COMMAND: CLICK
REF: {ref of target column}
ELEMENT: "{columnName}" from related table

EXAMPLES:
  "Name" from Customers
  "Price" from Products
  "Email" from Users
```

### Step 8: Verify Preview

```
COMMAND: SNAPSHOT
PURPOSE: Check lookup preview

VERIFY:
  - Values are being pulled from related table
  - Correct data showing (names, prices, etc.)
  - Rows without relations show empty (expected)
```

### Step 9: Save Column

```
COMMAND: CLICK
REF: {ref of save button}
ELEMENT: Save button

EXPECTED: Lookup column added with values from related table
```

---

## Common Lookup Patterns

### Orders → Customers

```
Table: Orders
Relation: Customer (links to Customers table)

Lookups to add:
  - Customer Name (lookup Name through Customer)
  - Customer Email (lookup Email through Customer)
  - Customer Phone (lookup Phone through Customer)
```

### Tasks → Projects

```
Table: Tasks
Relation: Project (links to Projects table)

Lookups to add:
  - Project Name (lookup Name through Project)
  - Project Status (lookup Status through Project)
  - Project Owner (lookup Owner through Project)
```

### OrderItems → Products

```
Table: OrderItems
Relation: Product (links to Products table)

Lookups to add:
  - Product Name (lookup Name through Product)
  - Unit Price (lookup Price through Product)
  - Product Image (lookup Image through Product)
```

---

## Error Recovery

### "No relations available"

```
ISSUE: Can't find any relations to select

CAUSE: Table doesn't have a relation column yet

FIX: 
  1. Cancel this procedure
  2. Use add-relation.md to create a relation first
  3. Return and create the lookup
```

### Lookup Shows Empty

```
ISSUE: Lookup column is empty for all rows

CHECK:
  - Does the relation column have values? (matching IDs)
  - Are the IDs matching correctly?
  - Is the source table populated?

DEBUG:
  - Check the relation column itself
  - Should show related row count or link
```

### Wrong Values Appearing

```
ISSUE: Lookup shows unexpected data

CHECK:
  - Selected the correct relation?
  - Selected the correct column from related table?
  - Relation matching the right records?

FIX: Delete and recreate with correct selections
```

### Lookup on Multi-Relation

```
ISSUE: Relation connects to multiple rows

NOTE: Lookup on a multi-relation returns the FIRST match only

FOR MULTIPLE VALUES:
  - Use Rollup for aggregations (count, sum)
  - Use Joined List to concatenate all values
  - Use Single Value to pick specific one (first/last)
```

---

## Tips

1. **Relation first** - Always create the relation before lookups
2. **Meaningful names** - Call it "Customer Name" not "Lookup 1"
3. **Multiple lookups OK** - Can have several lookups through same relation
4. **Lookup images** - Great for showing related product/user images
5. **Chain lookups** - Can lookup through a lookup (A→B→C) with multiple relations

### When to Use Lookup vs Other Columns

| Need | Use |
|------|-----|
| Single value from related row | **Lookup** |
| Count of related rows | Rollup |
| Sum/Average of related values | Rollup |
| All related values as list | Joined List |
| First or last related value | Single Value |

---

## Report Format

```
LOOKUP COLUMN ADDED:
  Table: {tableName}
  Column: {columnName}
  Through Relation: {relationColumnName}
  Looking Up: {sourceTableName}.{sourceColumnName}
  Status: SUCCESS | FAILED
  Sample Output: {example values}
```
