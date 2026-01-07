---
name: trigger-workflow-action
description: Add Trigger Workflow action to connect App Interaction to server-side workflow
preconditions:
  - Manual workflow exists
  - Screen/component exists to add action to
---

# Trigger Workflow Action Procedure

## Overview

Connects an App Interaction (client-side button/action) to a Manual server-side workflow. This bridges the gap between user actions and server-side processing.

## The Connection

```
App Screen                Server-Side
┌─────────────┐          ┌─────────────┐
│   Button    │   ───>   │   Manual    │
│   Click     │  passes  │  Workflow   │
│             │  data    │             │
└─────────────┘          └─────────────┘
```

## Prerequisites

Before adding this action:
1. **Manual workflow must exist** - Create it first with defined input variables
2. **Screen must exist** - The screen where the button/action lives
3. **Know what data to pass** - Match screen data to workflow input variables

## Procedure

### Step 1: Navigate to Layout Editor

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/layout

EXPECTED: Layout Editor loads
```

### Step 2: Navigate to Target Screen

Go to the screen where you want to add the trigger.

```
COMMAND: SNAPSHOT
PURPOSE: Find target screen

FIND: The tab/screen in Navigation sidebar
```

```
COMMAND: CLICK
REF: {ref of screen}
ELEMENT: "{screenName}" screen

EXPECTED: Screen shown in preview
```

### Step 3: Select or Add Button Component

**If button exists:**
```
COMMAND: CLICK
REF: {ref of button component}
ELEMENT: "{buttonName}" component

EXPECTED: Button selected, config in right panel
```

**If need to add button:**
Follow `screen-builder/procedures/components/add-component.md` to add a Button first.

### Step 4: Find Actions Configuration

```
COMMAND: SNAPSHOT
PURPOSE: Find actions section

FIND:
  - "Actions" section in right panel
  - "On Click" or "On Tap" configuration
  - Existing actions list
```

```
COMMAND: CLICK
REF: {ref of Actions section}
ELEMENT: Actions section

EXPECTED: Actions configuration visible
```

### Step 5: Add Trigger Workflow Action

```
COMMAND: SNAPSHOT
PURPOSE: Find add action button
```

```
COMMAND: CLICK
REF: {ref of add action button}
ELEMENT: Add action button

EXPECTED: Action type picker appears
```

```
COMMAND: SNAPSHOT
PURPOSE: Find Trigger Workflow option

FIND:
  - Search input
  - May be under "Integrations" or "Workflows" category
  - "Trigger Workflow" option
```

**Using search:**
```
COMMAND: TYPE
REF: {ref of search input}
ELEMENT: Action search
TEXT: trigger workflow
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of Trigger Workflow option}
ELEMENT: "Trigger Workflow" action

EXPECTED: Action added, configuration appears
```

### Step 6: Select Target Workflow

```
COMMAND: SNAPSHOT
PURPOSE: Find workflow selector

FIND:
  - Workflow dropdown
  - List of available Manual workflows
```

```
COMMAND: CLICK
REF: {ref of workflow dropdown}
ELEMENT: Workflow selector
```

```
COMMAND: SNAPSHOT
PURPOSE: Find target workflow

FIND: Manual workflows list (only Manual type shown)
```

```
COMMAND: CLICK
REF: {ref of target workflow}
ELEMENT: "{workflowName}" workflow

EXPECTED: Workflow selected, input variables appear
```

### Step 7: Map Input Variables

After selecting the workflow, its input variables appear. Map screen data to each variable.

```
COMMAND: SNAPSHOT
PURPOSE: Find input variable mappings

FIND:
  - List of input variables defined in the workflow
  - Data source selector for each variable
```

For each input variable:

```
COMMAND: CLICK
REF: {ref of variable data selector}
ELEMENT: "{variableName}" input mapping
```

```
COMMAND: SNAPSHOT
PURPOSE: Find screen data to map

FIND:
  - Current row columns (if on detail screen)
  - Screen values
  - User properties
```

```
COMMAND: CLICK
REF: {ref of data source}
ELEMENT: "{columnName}" or data source

EXPECTED: Variable mapped to screen data
```

**Repeat for each input variable.**

### Step 8: Configure Button Label (Optional)

```
COMMAND: SNAPSHOT
PURPOSE: Find button label input
```

```
COMMAND: TYPE
REF: {ref of label input}
ELEMENT: Button label
TEXT: {buttonLabel}

EXAMPLES: "Process Order", "Send Notification", "Generate Report"
```

### Step 9: Verify Configuration

```
COMMAND: SNAPSHOT
PURPOSE: Confirm trigger setup

VERIFY:
  - Correct workflow is selected
  - All input variables are mapped
  - Mappings point to correct data
  - Button label is appropriate
```

## Example Mapping

**Workflow: "Process Order"**
Input variables:
- `orderId`
- `customerEmail`
- `orderTotal`

**Screen: Order Detail**
Available columns:
- ID
- Customer Email
- Total

**Mapping:**
| Workflow Variable | Screen Data |
|-------------------|-------------|
| `orderId` | Current row → ID |
| `customerEmail` | Current row → Customer Email |
| `orderTotal` | Current row → Total |

## Common Patterns

### From Detail Screen

```
Button: "Mark Complete"
Workflow: "Update Status"
Mapping:
  - itemId = Current row → ID
  - newStatus = "Complete" (static value)
  - userId = Current user → ID
```

### From Collection Screen

```
Button: "Export All" (screen-level action)
Workflow: "Generate Export"
Mapping:
  - exportType = "full"
  - requestedBy = Current user → Email
```

### With User Input

```
Button: "Submit Request"
Workflow: "Create Request"
Mapping:
  - title = Text Entry component value
  - description = Text Entry component value
  - requester = Current user → ID
```

## Error Recovery

### Workflow Not Appearing

```
ISSUE: Can't find workflow in dropdown

CHECK:
  - Is the workflow a Manual type? (not Schedule/Webhook)
  - Is the workflow saved?
  - Are you in the correct app?

TRY: Refresh page, or go to Workflows and verify it exists as Manual type
```

### Input Variables Missing

```
ISSUE: No variables to map after selecting workflow

CHECK:
  - Does the workflow have input variables defined?
  - Were they saved?

FIX: Go to Workflows, edit the Manual workflow, add input variables
```

### Can't Map to Screen Data

```
ISSUE: Data source not available for mapping

CHECK:
  - Are you on a data-backed screen?
  - Does the column exist in this table?
  - Is it a computed column (may need base column)?

TRY: Check what data is actually available on this screen
```

### Action Not Executing

```
ISSUE: Button clicks but nothing happens

CHECK:
  - Is the workflow published/active?
  - Are all required mappings filled?
  - Check workflow run history for errors

DEBUG:
  - Go to Workflows
  - Check run history for the workflow
  - Look for error messages
```

## Best Practices

1. **Name workflows clearly** - "Process Order" not "Workflow 1"
2. **Map all variables** - Empty variables may cause issues
3. **Validate data types** - Number variable needs number data
4. **Test with real data** - Use preview mode to test action
5. **Add visual feedback** - Show notification after triggering

## Visual Feedback

After triggering a workflow, consider adding:
1. Show Notification action - "Processing your request..."
2. Navigate back - Return to list view
3. Refresh data - Update screen to show changes

## Report Format

```
TRIGGER WORKFLOW ACTION ADDED:
  Screen: {screenName}
  Component: {buttonName}
  Triggers: {workflowName}
  Mappings:
    - {variableName} ← {dataSource}
    - {variableName} ← {dataSource}
  Button Label: {label}
  Status: SUCCESS | FAILED
  Notes: {any issues}
```
