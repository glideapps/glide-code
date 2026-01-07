---
name: create-manual-workflow
description: Create a manual workflow triggered by app interactions
preconditions:
  - App exists
  - Browser authenticated
---

# Create Manual Workflow Procedure

## Overview

Creates a server-side workflow that is triggered by an App Interaction using the "Trigger Workflow" action. Allows passing data from the app to the workflow via input variables.

## Key Concept

Manual workflows bridge client-side actions to server-side processing:

```
App Interaction (client) → Trigger Workflow → Manual Workflow (server)
                          passes variables
```

## Procedure

### Step 1: Navigate to Workflows

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/workflows

EXPECTED: Workflow editor loads
```

### Step 2: Create New Workflow

```
COMMAND: SNAPSHOT
PURPOSE: Find new workflow button
```

```
COMMAND: CLICK
REF: {ref of new workflow button}
ELEMENT: New workflow button

EXPECTED: Workflow type selection appears
```

### Step 3: Select Manual Type

```
COMMAND: SNAPSHOT
PURPOSE: Find workflow type options
```

```
COMMAND: CLICK
REF: {ref of Manual option}
ELEMENT: "Manual" trigger type

EXPECTED: New manual workflow created
VERIFY: Right panel shows input variable configuration
```

### Step 4: Name the Workflow

```
COMMAND: SNAPSHOT
PURPOSE: Find workflow name input
```

```
COMMAND: CLICK
REF: {ref of name input}
ELEMENT: Workflow name input
```

```
COMMAND: TYPE
REF: {ref of name input}
ELEMENT: Workflow name
TEXT: {workflowName}

EXAMPLES: "Process Order", "Send Welcome Email", "Generate Report"
```

### Step 5: Define Input Variables

Input variables are the data passed from the App Interaction.

```
COMMAND: SNAPSHOT
PURPOSE: Find input variables section

FIND: "Input Variables" or "Parameters" section in right panel
      "Add variable" or "+" button
```

**Add a variable:**
```
COMMAND: CLICK
REF: {ref of add variable button}
ELEMENT: Add input variable button

EXPECTED: New variable row appears
```

```
COMMAND: SNAPSHOT
PURPOSE: Find variable name and type inputs
```

**Set variable name:**
```
COMMAND: TYPE
REF: {ref of variable name input}
ELEMENT: Variable name input
TEXT: {variableName}

NAMING RULES:
  - Use camelCase: orderId, customerEmail, productName
  - Be descriptive: use "customerId" not "id" or "var1"
  - Match what will be passed from app
```

**Set variable type (if available):**
```
COMMAND: CLICK
REF: {ref of type dropdown}
ELEMENT: Variable type selector
```

```
COMMAND: CLICK
REF: {ref of type option}
ELEMENT: "{type}" option

TYPES: Text, Number, Date, Boolean, etc.
```

**Repeat for each input variable needed.**

### Step 6: Common Input Variable Patterns

| Use Case | Variables to Define |
|----------|---------------------|
| **Order Processing** | `orderId`, `customerId`, `orderTotal` |
| **User Notification** | `userId`, `message`, `notificationType` |
| **Report Generation** | `reportType`, `startDate`, `endDate` |
| **Approval Flow** | `requestId`, `approverId`, `action` |
| **Data Sync** | `recordId`, `sourceTable`, `operation` |

### Step 7: Verify Input Variables

```
COMMAND: SNAPSHOT
PURPOSE: Confirm all variables are defined

VERIFY:
  - All needed variables are listed
  - Names are correct and descriptive
  - Types are appropriate (if set)
```

### Step 8: Use Variables in Workflow Nodes

When adding nodes to the workflow, these input variables are available as data sources.

**In any node's data binding:**
```
FIND: Variable/data selector
SELECT: One of the defined input variables

EXAMPLE: In "Send Email" node, bind "To" field to customerEmail variable
```

## Connecting from App Interaction

After creating the Manual workflow, an App Interaction must be configured to trigger it:

### In screen-builder (components/add-action.md):

1. Add a button or action to a screen
2. Add action: "Trigger Workflow"
3. Select this Manual workflow
4. Map screen data to input variables:
   - `orderId` ← Current row's Order ID column
   - `customerId` ← Current row's Customer ID column
   - etc.

## Example: Complete Flow

**Manual Workflow: "Send Order Confirmation"**

Input Variables:
- `orderId` (Text)
- `customerEmail` (Text)
- `customerName` (Text)

Workflow Nodes:
1. Trigger (Manual)
2. Query table: Get order details where ID = `orderId`
3. Send Email: To = `customerEmail`, personalized with `customerName`

**App Interaction (on Order detail screen):**
1. Button: "Send Confirmation"
2. Action: Trigger Workflow → "Send Order Confirmation"
3. Pass:
   - `orderId` = Current row's ID
   - `customerEmail` = Customer Email column
   - `customerName` = Customer Name column

## Error Recovery

### Variables Not Showing in Nodes

```
ISSUE: Input variables not available in node configuration

CHECK:
  - Are variables saved? (click outside input to save)
  - Did you select the right workflow?
  
TRY: Refresh page, variables should appear
```

### Workflow Not Triggering

```
ISSUE: App action executes but workflow doesn't run

CHECK:
  - Is the Trigger Workflow action pointing to correct workflow?
  - Are all required input variables being passed?
  - Does the workflow have nodes? (empty workflows may not execute)
```

### Variable Values Empty

```
ISSUE: Workflow runs but variable values are empty/null

CHECK:
  - Is the App Interaction correctly mapping data to variables?
  - Does the screen have access to that data?
  - Are column names correct in the mapping?
```

## Report Format

```
MANUAL WORKFLOW CREATED:
  Name: {workflowName}
  Input Variables:
    - {variableName}: {type}
    - {variableName}: {type}
  Nodes Added: {list}
  Status: SUCCESS | FAILED
  
INTEGRATION NOTE:
  Use "Trigger Workflow" action in App Interaction to call this workflow.
  Map screen data to input variables.
```
