---
name: add-relation
description: Create a relation column linking two tables
preconditions:
  - Both tables must exist
  - Must be in Data Editor
---

# Add Relation Procedure

## Overview

Creates a Relation column that links rows from one table to rows in another table. This is the foundation for Lookups and Rollups.

## Relation Types

| Type | Description | Example |
|------|-------------|---------|
| **Single Relation** | One row matches one row | Task → Project (each task belongs to one project) |
| **Multiple Relation** | One row matches many rows | Project → Tasks (one project has many tasks) |

## Preconditions

- Both tables exist and are linked to the app
- **Both tables have Row ID columns** (type: Basic → Row ID)
- Source table has a foreign key column (type: Text) to hold target table's Row ID
- You know which columns to match on (foreign key → Row ID)

**IMPORTANT**: Add Row ID columns to both tables BEFORE creating relations. See data-modeling skill for Row ID guidance.

## Procedure

### Step 1: Navigate to Source Table

The source table is where you'll add the relation column.

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/data

EXPECTED: Data Editor loads
```

```
COMMAND: SNAPSHOT
PURPOSE: Find source table
```

```
COMMAND: CLICK
REF: {ref of source table}
ELEMENT: "{sourceTableName}" in sidebar

EXPECTED: Table opens
```

### Step 2: Start Adding Column

```
COMMAND: PRESS_KEY
KEY: Meta+Shift+Enter (Mac) or Control+Shift+Enter (Windows)

EXPECTED: Column creation panel appears
```

### Step 3: Name the Relation Column

```
COMMAND: SNAPSHOT
PURPOSE: Find name input
```

```
COMMAND: TYPE
REF: {ref of name input}
ELEMENT: Column name input
TEXT: {relationColumnName}

NOTE: Use the target table name or a descriptive name
      e.g., "Project" for Tasks→Projects relation
      e.g., "Assigned Tasks" for User→Tasks relation
```

### Step 4: Select Relation Type

```
COMMAND: SNAPSHOT
PURPOSE: Find type dropdown
```

```
COMMAND: CLICK
REF: {ref of type dropdown}
ELEMENT: Type dropdown

EXPECTED: Type picker opens
```

```
COMMAND: SNAPSHOT
PURPOSE: Find search input
```

```
COMMAND: CLICK
TEXT: Relation

EXPECTED: Shows 'Relation' in the Type dropdown selected.
```

### Step 5: Configure Relation - Select Target Table

```
COMMAND: SNAPSHOT
PURPOSE: Find target table selector

FIND: Dropdown labeled "Table" or showing a table name
      This selects which table to relate TO
```

```
COMMAND: CLICK
REF: {ref of table dropdown}
ELEMENT: Target table dropdown

EXPECTED: List of available tables appears
```

```
COMMAND: SNAPSHOT
PURPOSE: Find target table in list
```

```
COMMAND: CLICK
REF: {ref of target table}
ELEMENT: "{targetTableName}" in table list

EXPECTED: Target table selected
```

### Step 6: Configure Relation - Set Match Columns

Now you configure HOW rows are matched.

```
COMMAND: SNAPSHOT
PURPOSE: Find match configuration

FIND: Two dropdown areas:
      1. "Match" or source column selector (column from current table)
      2. "With" or target column selector (column from target table)
```

**Source Column (from current table):**

```
COMMAND: CLICK
REF: {ref of source column dropdown}
ELEMENT: Source column selector ("Match" dropdown)

EXPECTED: List of columns from source table
```

```
COMMAND: SNAPSHOT
PURPOSE: Find source column
```

```
COMMAND: CLICK
REF: {ref of source match column}
ELEMENT: "{sourceMatchColumn}" column

EXPECTED: Source column selected
NOTE: This is usually a foreign key column like "projectId"
```

**Target Column (from target table):**

```
COMMAND: CLICK
REF: {ref of target column dropdown}
ELEMENT: Target column selector ("With" dropdown)

EXPECTED: List of columns from target table
```

```
COMMAND: SNAPSHOT
PURPOSE: Find target column
```

```
COMMAND: CLICK
REF: {ref of target match column}
ELEMENT: "{targetMatchColumn}" column

EXPECTED: Target column selected
NOTE: **BEST PRACTICE**: Select the explicit "Row ID" column you created
      - Look for the Row ID column (type: Row ID) you added to the target table
      - Avoid using auto-generated "$rowID" - use explicit Row ID columns instead
      - This provides more reliable, stable relations
```

### Step 7: Set Relation Cardinality (Optional)

Some UIs let you specify if this is a single or multiple relation.

```
COMMAND: SNAPSHOT
PURPOSE: Check for cardinality option

FIND: Toggle or radio buttons for "Match one" vs "Match multiple"
      Or "Single" vs "Multiple"
```

If found:
```
COMMAND: CLICK
REF: {ref of appropriate option}
ELEMENT: "{single|multiple}" relation option

NOTE: 
  - Single: When source column matches exactly one target row (many-to-one)
  - Multiple: When source column might match many target rows (one-to-many)
```

### Step 8: Save Relation

```
COMMAND: SNAPSHOT
PURPOSE: Find save button
```

```
COMMAND: CLICK
REF: {ref of save button}
ELEMENT: Save button

EXPECTED: Panel closes, relation column appears
VERIFY: Column shows link/relation icon
```

### Step 9: Verify Relation Works

```
COMMAND: SNAPSHOT
PURPOSE: Check relation column shows linked data

VERIFY:
  - Column appears in table header with relation icon
  - Cells show linked row data (or empty if no match)
  - Clicking a cell shows the related row(s)
```

## Common Relation Patterns

### Pattern 1: Task belongs to Project (Many-to-One)

```
SETUP:
  Projects table: Add "Row ID" column (type: Row ID)
  Tasks table: Add "Row ID" column (type: Row ID)
  Tasks table: Add "projectId" column (type: Text) to hold Project Row ID

RELATION:
  Source Table: Tasks
  Relation Column Name: Project
  Target Table: Projects
  Match: Tasks.projectId (Text) → Projects.Row ID (Row ID)
  Type: Single relation
```

### Pattern 2: Project has many Tasks (One-to-Many)

```
SETUP:
  Projects table: Has "Row ID" column
  Tasks table: Has "Row ID" column and "projectId" foreign key

RELATION:
  Source Table: Projects
  Relation Column Name: Tasks
  Target Table: Tasks
  Match: Projects.Row ID (Row ID) → Tasks.projectId (Text)
  Type: Multiple relation
```

### Pattern 3: User assigned to Tasks (One-to-Many)

```
SETUP:
  Users table: Add "Row ID" column (type: Row ID)
  Tasks table: Add "Row ID" column (type: Row ID)
  Tasks table: Add "assigneeId" column (type: Text) to hold User Row ID
  (Or use email if that's the unique identifier)

RELATION:
  Source Table: Users
  Relation Column Name: Assigned Tasks
  Target Table: Tasks
  Match: Users.Row ID (Row ID) → Tasks.assigneeId (Text)
  OR: Users.email (Email) → Tasks.assigneeEmail (Email)
  Type: Multiple relation
```

## Error Recovery

### Target Table Not in List

```
ISSUE: The table you want to relate to isn't showing in the dropdown

CHECK:
  1. Is the table linked to this app?
  2. Is it a Big Table that needs to be linked first?

FIX: 
  - Go to Data Sources in sidebar
  - Click team name
  - Link the table to this app
  - Retry adding relation
```

### No Matching Data Shows

```
ISSUE: Relation column is empty even though data should match

CHECK:
  1. Are the match column values actually equal?
  2. Is there a data type mismatch (string "123" vs number 123)?
  3. Are there extra spaces in the data?

DEBUG:
  - Look at actual values in both columns
  - Try matching on a different column
```

### Wrong Relation Direction

```
ISSUE: Relation shows unexpected data

The direction matters:
  - If you want to see "which project this task belongs to"
    → Add relation to Tasks table, pointing to Projects
  
  - If you want to see "which tasks belong to this project"
    → Add relation to Projects table, pointing to Tasks

FIX: Delete the relation and recreate with correct direction
```

## Report Format

```
RELATION CREATED:
  Source Table: {sourceTableName}
  Column Name: {relationColumnName}
  Target Table: {targetTableName}
  Match: {sourceColumn} → {targetColumn}
  Type: Single | Multiple
  Status: SUCCESS | FAILED
  Verification: {shows linked data | no matches found | error}
```
