---
name: add-condition-node
description: Add a condition node for branching logic
preconditions:
  - Workflow exists
---

# Add Condition Node Procedure

## Overview

Adds a Condition node (also called a branch) to create different execution paths based on criteria. Conditions are evaluated **left-to-right** - the first matching condition is executed, and others are skipped.

## Key Concept: Left-to-Right Evaluation

```
Condition Node
    ├── Path 1 (checked first)
    ├── Path 2 (checked if Path 1 fails)
    ├── Path 3 (checked if Path 1 & 2 fail)
    └── Else (if no conditions match)
```

**Important**: Once a condition matches, paths to the right are NOT evaluated.

## Procedure

### Step 1: Ensure in Workflow Editor

```
COMMAND: SNAPSHOT
PURPOSE: Verify in workflow canvas

VERIFY:
  - Workflow editor is open
  - Canvas shows existing nodes
```

### Step 2: Add Condition Node

```
COMMAND: SNAPSHOT
PURPOSE: Find add node option

FIND: 
  - "+" button on canvas
  - Connection point from existing node
```

```
COMMAND: CLICK
REF: {ref of add node button}
ELEMENT: Add node button (+)

EXPECTED: Node picker appears
```

### Step 3: Find Condition in Picker

```
COMMAND: SNAPSHOT
PURPOSE: Find Condition node

FIND:
  - Search input
  - "Logic" or "Control" category
  - "Condition" or "Branch" or "If" option
```

**Using search:**
```
COMMAND: TYPE
REF: {ref of search input}
ELEMENT: Node search
TEXT: condition

EXPECTED: Condition node appears
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of Condition node}
ELEMENT: "Condition" node option

EXPECTED: Condition node added with multiple paths
```

### Step 4: Configure First Condition (Leftmost Path)

The leftmost path is checked first.

```
COMMAND: SNAPSHOT
PURPOSE: Find condition configuration

FIND:
  - First branch/path configuration
  - "If" condition input
  - May click on the path itself to configure
```

```
COMMAND: CLICK
REF: {ref of first path or condition config}
ELEMENT: First condition path

EXPECTED: Condition editor appears in right panel
```

**Set the condition:**

```
COMMAND: SNAPSHOT
PURPOSE: Find condition builder

FIND:
  - Column/variable selector
  - Operator dropdown
  - Value input
```

**Select what to check:**
```
COMMAND: CLICK
REF: {ref of data selector}
ELEMENT: Data to check
```

```
COMMAND: CLICK
REF: {ref of column/variable}
ELEMENT: "{dataSource}" 

EXAMPLES: 
  - Column from trigger data
  - Variable from previous node
  - Input variable (for Manual workflows)
```

**Select operator:**
```
COMMAND: CLICK
REF: {ref of operator dropdown}
ELEMENT: Condition operator
```

```
COMMAND: CLICK
REF: {ref of operator}
ELEMENT: "{operator}"

OPERATORS:
  - is / equals
  - is not / not equals
  - contains
  - is empty / is not empty
  - greater than / less than
  - greater than or equal / less than or equal
```

**Set comparison value:**
```
COMMAND: TYPE or SELECT
REF: {ref of value input}
ELEMENT: Comparison value
TEXT: {value}

EXAMPLES: "Approved", "Complete", 100, true
```

### Step 5: Add Nodes to First Path

```
COMMAND: SNAPSHOT
PURPOSE: Find add node in first path

FIND: 
  - "+" button inside/below first condition path
  - Drop zone for the "true" case
```

```
COMMAND: CLICK
REF: {ref of add node in path}
ELEMENT: Add node to first path

EXPECTED: Node picker appears
```

Add the nodes that should execute when this condition is true.

### Step 6: Add More Conditions (Additional Paths)

To add more conditions (Else If):

```
COMMAND: SNAPSHOT
PURPOSE: Find add condition/path button

FIND:
  - "Add path" or "Add condition" button
  - "+" at the condition level (not inside a path)
```

```
COMMAND: CLICK
REF: {ref of add path button}
ELEMENT: Add condition path

EXPECTED: New condition path added to the RIGHT
```

Configure this new condition the same way as Step 4.

**Remember**: This condition is only checked if the one(s) to its left failed!

### Step 7: Configure Else Path (Optional)

The Else path executes when NO conditions match.

```
COMMAND: SNAPSHOT
PURPOSE: Find Else path

FIND:
  - "Else" or "Default" path (usually rightmost)
  - May be automatically present or need to enable
```

Add nodes to the Else path for fallback behavior.

### Step 8: Order Matters - Verify Condition Order

```
COMMAND: SNAPSHOT
PURPOSE: Review condition order (left to right)

VERIFY conditions are ordered correctly:
  1. Most specific conditions FIRST (leftmost)
  2. More general conditions later
  3. Else/default LAST (rightmost)
```

**Example - Good order:**
```
1. status = "Urgent"     → Handle urgent immediately
2. status = "High"       → Handle high priority
3. status = "Normal"     → Standard processing
4. Else                  → Log unknown status
```

**Example - Bad order:**
```
1. status contains ""    → This matches EVERYTHING! (too broad)
2. status = "Urgent"     → Never reached!
```

### Step 9: Verify Configuration

```
COMMAND: SNAPSHOT
PURPOSE: Confirm condition setup

VERIFY:
  - Conditions are in correct left-to-right order
  - Each path has the right nodes
  - Else path handles unexpected cases
  - No overlapping conditions that could cause confusion
```

## Condition Patterns

### Status-Based Routing

```
Path 1: status = "Approved" → Update row, notify user
Path 2: status = "Rejected" → Send rejection email
Path 3: status = "Pending"  → Add to review queue
Else:   Log unknown status
```

### Amount Thresholds

```
Path 1: amount > 10000     → Require approval
Path 2: amount > 1000      → Manager notification
Path 3: amount > 0         → Auto-approve
Else:   Handle zero/negative
```

### User Type Routing

```
Path 1: userRole = "Admin"  → Full access flow
Path 2: userRole = "Manager" → Limited flow
Path 3: userRole = "User"   → Basic flow
Else:   Deny access
```

### Error Handling

```
Path 1: success = true     → Continue normal flow
Path 2: errorType = "rate_limit" → Wait and retry
Path 3: errorType = "auth"  → Refresh token
Else:   Log error and alert
```

## Multiple Conditions in One Path

Some condition nodes allow AND/OR logic within a single path:

```
COMMAND: SNAPSHOT
PURPOSE: Find "AND" or "OR" option

FIND:
  - "Add condition" within a path (not a new path)
  - AND/OR toggle
```

**Example - All must be true (AND):**
```
status = "Approved" AND amount > 100 AND isActive = true
```

**Example - Any can be true (OR):**
```
priority = "High" OR priority = "Urgent"
```

## Error Recovery

### Condition Never Matches

```
ISSUE: Always going to Else path

CHECK:
  - Is the data type correct? (string vs number)
  - Are values spelled exactly right?
  - Is the operator correct? (= vs contains)
  - Is the data actually reaching the condition?

DEBUG: Add a logging node before condition to see values
```

### Wrong Path Executing

```
ISSUE: Going to unexpected path

CHECK:
  - Remember: left-to-right, first match wins
  - Is there a broader condition to the left catching it?
  - Are conditions overlapping?

FIX: Reorder conditions, most specific first
```

### Can't Add More Paths

```
ISSUE: No option to add additional conditions

CHECK:
  - Is there a limit on number of paths?
  - Look for "+" at the condition node level, not inside a path

TRY: Nest another condition node inside a path
```

## Best Practices

1. **Order specifically** - Most specific conditions first
2. **Always have Else** - Handle unexpected cases
3. **Don't overlap** - Conditions should be mutually exclusive when possible
4. **Document logic** - Name paths descriptively
5. **Test each path** - Verify all paths work as expected

## Report Format

```
CONDITION NODE ADDED:
  Workflow: {workflowName}
  Paths (left to right):
    1. IF {condition} → {nodes}
    2. ELSE IF {condition} → {nodes}
    3. ELSE → {nodes}
  Status: SUCCESS | FAILED
  Notes: {any issues}
```
