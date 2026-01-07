---
name: add-loop-node
description: Add a loop node to iterate over table rows
preconditions:
  - Workflow exists (server-side only)
  - At least one table exists in the app
---

# Add Loop Node Procedure

## Overview

Adds a Loop node to a server-side workflow. Loops iterate over rows in a table and execute child nodes for each row.

**⚠️ Server-Side Only** - Loops are only available in server-side workflows (Schedule, Webhook, Manual, Email, Slack). They are NOT available in App Interactions.

## Loop Capabilities

- Select a table to iterate over
- Filter which rows to process
- Access current row data in each iteration
- Nest other nodes inside the loop

## Procedure

### Step 1: Ensure in Workflow Editor

```
COMMAND: SNAPSHOT
PURPOSE: Verify in workflow canvas

VERIFY:
  - Workflow editor is open
  - This is a server-side workflow
  - Canvas shows trigger node and any existing nodes
```

### Step 2: Add Loop Node

```
COMMAND: SNAPSHOT
PURPOSE: Find add node option

FIND: 
  - "+" button on canvas or between nodes
  - Node palette/picker
  - Drag point from existing node
```

**Method A: Click add button**
```
COMMAND: CLICK
REF: {ref of add node button}
ELEMENT: Add node button (+)

EXPECTED: Node picker appears
```

**Method B: Drag from existing node**
```
COMMAND: DRAG
FROM_REF: {ref of node output connector}
FROM_ELEMENT: Node output
TO_REF: {ref of empty canvas area}
TO_ELEMENT: Canvas

EXPECTED: Node picker appears
```

### Step 3: Find Loop in Picker

```
COMMAND: SNAPSHOT
PURPOSE: Find Loop node option

FIND: 
  - Search input
  - Categories (may be under "Logic" or "Control")
  - "Loop" or "For Each" option
```

**Using search:**
```
COMMAND: TYPE
REF: {ref of search input}
ELEMENT: Node search
TEXT: loop

EXPECTED: Loop node appears in results
```

```
COMMAND: SNAPSHOT
PURPOSE: Find Loop node
```

```
COMMAND: CLICK
REF: {ref of Loop node}
ELEMENT: "Loop" node option

EXPECTED: Loop node added to canvas
```

### Step 4: Select Table to Loop Over

```
COMMAND: SNAPSHOT
PURPOSE: Find table selector in Loop configuration

FIND: 
  - Right panel with Loop properties
  - Table/source dropdown
  - May be labeled "Table", "Source", or "Data"
```

```
COMMAND: CLICK
REF: {ref of table dropdown}
ELEMENT: Table selector
```

```
COMMAND: SNAPSHOT
PURPOSE: Find target table
```

```
COMMAND: CLICK
REF: {ref of target table}
ELEMENT: "{tableName}" table

EXPECTED: Table selected, loop will iterate over its rows
```

### Step 5: Configure Row Filter (Important!)

**⚠️ Always filter loops to avoid processing entire tables.**

```
COMMAND: SNAPSHOT
PURPOSE: Find filter configuration

FIND:
  - "Filter" or "Conditions" section
  - "Add filter" button
  - Filter rules area
```

```
COMMAND: CLICK
REF: {ref of add filter button}
ELEMENT: Add filter button

EXPECTED: Filter configuration appears
```

**Configure filter condition:**

```
COMMAND: SNAPSHOT
PURPOSE: Find filter column/value inputs
```

```
COMMAND: CLICK
REF: {ref of column dropdown}
ELEMENT: Filter column selector
```

```
COMMAND: CLICK
REF: {ref of column}
ELEMENT: "{columnName}" column

EXAMPLE: "status" column
```

```
COMMAND: CLICK
REF: {ref of operator dropdown}
ELEMENT: Filter operator
```

```
COMMAND: CLICK
REF: {ref of operator}
ELEMENT: "{operator}" 

OPERATORS: is, is not, contains, is empty, greater than, etc.
```

```
COMMAND: TYPE or SELECT
REF: {ref of value input}
ELEMENT: Filter value
TEXT: {value}

EXAMPLE: "Pending" to only process pending items
```

### Step 6: Add Nodes Inside Loop

Nodes placed inside the loop execute for each row.

```
COMMAND: SNAPSHOT
PURPOSE: Find loop body / inner area

FIND: 
  - Loop node may expand to show inner area
  - "+" button inside the loop
  - Drop zone for child nodes
```

```
COMMAND: CLICK
REF: {ref of add node inside loop}
ELEMENT: Add node inside loop

EXPECTED: Node picker appears
```

Add nodes as needed (Update Row, Send Email, etc.). These nodes can reference:
- **Current row data** - Columns from the row being processed
- **Loop variables** - Index, count, etc.

### Step 7: Using Current Row Data

When configuring nodes inside the loop:

```
COMMAND: SNAPSHOT
PURPOSE: Find data binding in inner node

FIND: Data/variable selector in node properties
```

```
COMMAND: CLICK
REF: {ref of data selector}
ELEMENT: Data source selector
```

```
COMMAND: SNAPSHOT
PURPOSE: Find loop row variables

FIND:
  - "Current row" or loop context
  - Columns from the selected table
  - Loop iteration info
```

```
COMMAND: CLICK
REF: {ref of current row column}
ELEMENT: Current row → {columnName}

EXPECTED: Node uses data from current iteration's row
```

### Step 8: Verify Loop Configuration

```
COMMAND: SNAPSHOT
PURPOSE: Confirm loop setup

VERIFY:
  - Table is selected
  - Filter is configured (not processing all rows!)
  - Child nodes are inside the loop
  - Data bindings use current row context
```

## Common Loop Patterns

### Update All Matching Rows

```
Loop: Over "Orders" table
Filter: status = "Pending" AND createdDate < 7 days ago

Inside Loop:
  - Update Row: Set status = "Expired"
```

### Send Notification to Each User

```
Loop: Over "Users" table
Filter: notifications = true

Inside Loop:
  - Send Email: To = Current row → email
                Subject = "Weekly Update"
```

### Sync Data to External System

```
Loop: Over "Products" table
Filter: syncStatus = "Pending"

Inside Loop:
  - HTTP Request: POST to external API with product data
  - Update Row: Set syncStatus = "Complete"
```

### Calculate Aggregates (Advanced)

```
Loop: Over "LineItems" table
Filter: orderId = {workflow variable}

Inside Loop:
  - Set Variable: total = total + Current row → amount
  
After Loop:
  - Update Order row with calculated total
```

## Error Recovery

### Loop Not Available

```
ISSUE: Can't find Loop node option

CHECK:
  - Is this a server-side workflow? (not App Interaction)
  - Look under "Logic" or "Control Flow" category

NOTE: Loops are only in Schedule, Webhook, Manual, Email, Slack workflows
```

### Table Not Appearing

```
ISSUE: Target table not in dropdown

CHECK:
  - Does the table exist?
  - Is the table linked to this app?

TRY: Refresh page, or check Data Editor
```

### Loop Timing Out

```
ISSUE: Workflow fails or times out

CHECK:
  - Is the filter too broad? (processing too many rows)
  - Are there nested loops? (exponential complexity)
  - Are inner nodes making slow external calls?

FIX: Add more restrictive filters
     Limit rows processed per run
```

### Can't Access Row Data

```
ISSUE: Current row columns not showing

CHECK:
  - Is the table selected in the loop?
  - Are you inside the loop node, not outside?

TRY: Click on a node inside the loop, then check data bindings
```

## Best Practices

1. **Always filter** - Never loop over entire tables
2. **Test with few rows** - Add `LIMIT 5` equivalent filter first
3. **Watch execution time** - Loops with external calls are slow
4. **Consider batching** - For large datasets, process in chunks
5. **Log progress** - Add logging nodes to track iterations

## Report Format

```
LOOP NODE ADDED:
  Workflow: {workflowName}
  Loop Over: {tableName}
  Filter: {filter description}
  Child Nodes: {list of nodes inside loop}
  Status: SUCCESS | FAILED
  Notes: {any issues}
```
