---
name: rollup-column
description: Add a Rollup column to aggregate values from related rows
preconditions:
  - Table has an existing Relation column (typically multi-relation)
  - Must be in Data Editor
---

# Rollup Column Procedure

Aggregates values from multiple related rows through a relation. Perfect for counts, sums, averages, and other aggregate calculations.

## Use Cases

- Count of orders per customer
- Sum of order totals for each customer
- Average rating per product
- Minimum/maximum price in a category
- Count of tasks per project

## Prerequisites

⚠️ **You must have a Relation column first unless you're rolling up the values of the whole table!**

Rollups aggregate data FROM related rows THROUGH a relation. The relation should typically be a "multi-relation" (one-to-many).

NOTE: Relations have a right angle arrow next to them in the list. Queries have magnifying glass. Full tables have a grid.

```
[Customers] ──multi-relation──> [Orders]
    │
    └── Rollup: Order Count (COUNT of related Orders)
    └── Rollup: Total Spent (SUM of Amount from Orders)
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
PURPOSE: Find and select the table that will SHOW the rollup
```

```
COMMAND: CLICK
REF: {ref of table in sidebar}
ELEMENT: "{tableName}" in table list

EXAMPLE: Click "Customers" if you want to see order counts per customer
```

### Step 2: Verify Relation Exists

```
COMMAND: SNAPSHOT
PURPOSE: Confirm a relation column exists (preferably multi-relation)

FIND: A column with relation icon that shows multiple related items

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
  "Order Count" (if counting orders)
  "Total Revenue" (if summing amounts)
  "Average Rating" (if averaging ratings)
  "Task Count" (if counting tasks)
  
TIP: Name describes the aggregation result
```

### Step 5: Select Rollup Type

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
REF: {ref of Rollup option}
ELEMENT: "Rollup Column" option

EXPECTED: Rollup configuration panel appears
```

### Step 6: Select the Relation

```
COMMAND: SNAPSHOT
PURPOSE: Find relation selector

FIND: Dropdown to select which relation to rollup from
```

```
COMMAND: CLICK
REF: {ref of relation dropdown}
ELEMENT: Relation selector
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of target relation}
ELEMENT: "{relationColumnName}" relation

EXAMPLE: Select "Orders" relation to aggregate order data
```

### Step 7: Select Aggregation Type

```
COMMAND: SNAPSHOT
PURPOSE: Find aggregation type selector

FIND: Dropdown or buttons for: Count, Sum, Average, Min, Max, etc.
```

```
COMMAND: CLICK
REF: {ref of aggregation dropdown}
ELEMENT: Aggregation type selector
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of aggregation type}
ELEMENT: "{aggregationType}" option

AGGREGATION TYPES:
  - Count: Number of related rows
  - Sum: Total of a numeric column
  - Average: Mean of a numeric column
  - Min: Smallest value
  - Max: Largest value
  - Count Unique: Distinct values count
```

### Step 8: Select Column to Aggregate (if not Count)

For Sum, Average, Min, Max - you need to pick which column:

```
COMMAND: SNAPSHOT
PURPOSE: Find column selector (if applicable)

NOTE: "Count" doesn't need a column - it just counts rows
      Other aggregations need a numeric column to operate on
```

If aggregation requires a column:

```
COMMAND: CLICK
REF: {ref of column dropdown}
ELEMENT: Column to aggregate selector
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of target column}
ELEMENT: "{columnName}" from related table

EXAMPLES:
  "Amount" for Sum of order amounts
  "Rating" for Average rating
  "Price" for Min/Max price
```

### Step 9: Verify Preview

```
COMMAND: SNAPSHOT
PURPOSE: Check rollup preview

VERIFY:
  - Aggregated values look correct
  - Counts match expected number of related items
  - Sums/averages are reasonable
  - Rows with no relations show 0 or empty
```

### Step 10: Save Column

```
COMMAND: CLICK
REF: {ref of save button}
ELEMENT: Save button

EXPECTED: Rollup column added with aggregated values
```

---

## Aggregation Types Reference

| Type | Use For | Result |
|------|---------|--------|
| **Count** | How many related items | Number (integer) |
| **Sum** | Total of numeric values | Number |
| **Average** | Mean of numeric values | Number (decimal) |
| **Min** | Smallest value | Same as source type |
| **Max** | Largest value | Same as source type |
| **Count Unique** | Distinct value count | Number (integer) |

---

## Common Rollup Patterns

### Customer Metrics

```
Table: Customers
Relation: Orders (multi-relation to Orders table)

Rollups:
  - Order Count (COUNT through Orders)
  - Total Spent (SUM of Amount through Orders)
  - Average Order (AVERAGE of Amount through Orders)
  - First Order Date (MIN of OrderDate through Orders)
  - Last Order Date (MAX of OrderDate through Orders)
```

### Project Dashboard

```
Table: Projects
Relation: Tasks (multi-relation to Tasks table)

Rollups:
  - Total Tasks (COUNT through Tasks)
  - Completed Tasks (COUNT where Status="Complete")
  - Total Hours (SUM of Hours through Tasks)
  - Avg Task Duration (AVERAGE of Duration through Tasks)
```

### Product Analytics

```
Table: Products
Relation: Reviews (multi-relation to Reviews table)

Rollups:
  - Review Count (COUNT through Reviews)
  - Average Rating (AVERAGE of Rating through Reviews)
  - Highest Rating (MAX of Rating through Reviews)
  - Lowest Rating (MIN of Rating through Reviews)
```

### Category Summary

```
Table: Categories
Relation: Products (multi-relation to Products table)

Rollups:
  - Product Count (COUNT through Products)
  - Price Range Low (MIN of Price through Products)
  - Price Range High (MAX of Price through Products)
  - Avg Product Price (AVERAGE of Price through Products)
```

---

## Error Recovery

### "No relations available"

```
ISSUE: Can't find any relations to select

CAUSE: Table doesn't have a relation column

FIX:
  1. Cancel this procedure
  2. Use add-relation.md to create a relation first
  3. Return and create the rollup
```

### Rollup Shows 0 or Empty

```
ISSUE: All rollup values are 0 or empty

CHECK:
  - Does the relation actually have related rows?
  - Are the IDs matching correctly?
  - Is the related table populated with data?

DEBUG:
  - Check relation column - does it show related items?
  - Verify related table has data
```

### Sum/Average Shows Wrong Value

```
ISSUE: Aggregated value seems incorrect

CHECK:
  - Is the source column numeric? (not text)
  - Are there empty/null values affecting calculation?
  - Selected the right column to aggregate?

NOTE: Empty values are typically excluded from Average
```

### "Column not compatible"

```
ISSUE: Can't select certain columns for Sum/Average

CAUSE: Only numeric columns can be summed/averaged

FIX: 
  - Use Count instead (works on any column)
  - Convert source column to numeric type first
```

---

## Tips

1. **Count is most common** - Start here for "how many related items"
2. **Relation direction matters** - Rollup from the "one" side of one-to-many
3. **Combine with If-Then-Else** - Show "No orders" if count is 0
4. **Use for dashboards** - Great for summary metrics
5. **Performance** - Rollups update automatically when related data changes

### Rollup + If-Then-Else Pattern

After creating a rollup, often add an If-Then-Else:

```
IF OrderCount = 0 THEN "New Customer"
ELSE IF OrderCount > 10 THEN "VIP Customer"
ELSE "Regular Customer"
```

### When to Use Rollup vs Other Columns

| Need | Use |
|------|-----|
| Count of related rows | **Rollup (Count)** |
| Sum of related values | **Rollup (Sum)** |
| Single value from related row | Lookup |
| All related values as text | Joined List |
| Specific related value (first/last) | Single Value |

---

## Report Format

```
ROLLUP COLUMN ADDED:
  Table: {tableName}
  Column: {columnName}
  Through Relation: {relationColumnName}
  Aggregation: {COUNT | SUM | AVERAGE | MIN | MAX}
  Aggregating Column: {columnName or "N/A for Count"}
  Status: SUCCESS | FAILED
  Sample Output: {example values}
```
