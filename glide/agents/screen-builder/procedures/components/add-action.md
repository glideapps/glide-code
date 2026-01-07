---
name: add-action
description: Add actions to components (buttons, navigation, data operations)
preconditions:
  - Screen and component exist
  - Must be on correct screen
---

# Add Action Procedure

## Overview

Adds actions to components. Actions can navigate to screens, modify data, open URLs, send messages, and more.

## Action Types

| Category | Actions |
|----------|---------|
| **Navigation** | Show New Screen, Show Form Screen, Go to Tab |
| **Data** | Add Row, Set Values, Delete Row |
| **Communication** | Compose Email, Compose SMS, Phone Call |
| **Other** | Open Link, Copy to Clipboard, Show Notification |

## Where Actions Can Be Added

- **Button components** - Primary use case
- **Collection items** - On tap/click
- **Form submit** - After form submission
- **Action Row** - List-style action items
- **Image/components** - On tap

## Procedure

### Step 1: Navigate to Correct Screen

(Follow screen navigation from `add-component.md`)

### Step 2: Select Component to Add Action To

```
COMMAND: SNAPSHOT
PURPOSE: Find target component

FIND: Component that should have the action
      (Button, Action Row, collection item, etc.)
```

```
COMMAND: CLICK
REF: {ref of component}
ELEMENT: "{componentName}" component

EXPECTED: Component selected, right panel shows settings
```

### Step 3: Find Actions Section

```
COMMAND: SNAPSHOT
PURPOSE: Find actions configuration

FIND: "Actions" section or "On Click" / "On Tap" option
      Usually in right panel configuration
      May be a tab or expandable section
```

```
COMMAND: CLICK
REF: {ref of Actions section}
ELEMENT: Actions section or tab

EXPECTED: Actions configuration area shown
```

### Step 4: Add New Action

```
COMMAND: SNAPSHOT
PURPOSE: Find add action button

FIND: "Add action" button or "+" in actions area
```

```
COMMAND: CLICK
REF: {ref of add action button}
ELEMENT: Add action button

EXPECTED: Action type selector appears
```

### Step 5: Select Action Type

```
COMMAND: SNAPSHOT
PURPOSE: Find action type options

FIND: List or menu of action types
```

```
COMMAND: CLICK
REF: {ref of desired action type}
ELEMENT: "{actionType}" action

EXPECTED: Action added, configuration options appear
```

### Step 6: Configure the Action

Configuration depends on action type:

#### Show New Screen

```
COMMAND: SNAPSHOT
PURPOSE: Find screen configuration

FIND: Screen source/type selector
```

```
COMMAND: CLICK
REF: {ref of screen source}
ELEMENT: Screen source selector
```

Options:
- **This item** - Detail view of current row
- **Another table** - Different data source
- **Current screen** - Stay on same screen

#### Set Values (Update Data)

```
COMMAND: SNAPSHOT
PURPOSE: Find value configuration

FIND: Column and value selectors
```

```
COMMAND: CLICK
REF: {ref of column selector}
ELEMENT: Column to set
```

```
COMMAND: CLICK
REF: {ref of target column}
ELEMENT: "{columnName}" column
```

```
COMMAND: SNAPSHOT
PURPOSE: Find value input
```

Set the value:
```
COMMAND: TYPE
REF: {ref of value input}
ELEMENT: Value input
TEXT: {newValue}

OR select from options if it's a column reference
```

#### Delete Row

```
Usually no additional configuration needed.
May have confirmation toggle.
```

#### Open Link

```
COMMAND: SNAPSHOT
PURPOSE: Find URL configuration
```

```
COMMAND: TYPE
REF: {ref of URL input}
ELEMENT: URL input
TEXT: {url}

OR bind to a URL column
```

#### Compose Email

```
COMMAND: SNAPSHOT
PURPOSE: Find email configuration
```

Configure:
- To: Email column or specific address
- Subject: Text or template
- Body: Text or template

```
COMMAND: CLICK
REF: {ref of "To" field}
ELEMENT: Email "To" field
```

```
COMMAND: CLICK
REF: {ref of email column}
ELEMENT: "{emailColumn}" column
```

### Step 7: Configure Action Appearance (for Buttons)

```
COMMAND: SNAPSHOT
PURPOSE: Find button text/icon options

FIND: "Label" or "Title" input for button text
      Icon selector
```

```
COMMAND: TYPE
REF: {ref of label input}
ELEMENT: Action/Button label
TEXT: {buttonLabel}

EXAMPLES: "Save", "Delete", "Send Email", "Mark Complete"
```

### Step 8: Order Multiple Actions

If multiple actions on same component:

```
COMMAND: SNAPSHOT
PURPOSE: See action order

NOTE: Top action in list = Leftmost button in UI
```

To reorder:
```
COMMAND: DRAG
FROM_REF: {ref of action to move}
FROM_ELEMENT: "{actionName}" action
TO_REF: {ref of target position}
TO_ELEMENT: Position {above|below} "{otherAction}"

EXPECTED: Actions reordered
```

### Step 9: Verify Action

```
COMMAND: SNAPSHOT
PURPOSE: Confirm action configured

VERIFY:
  - Action appears in actions list
  - Button shows correct label in preview
  - Configuration is complete (no warnings)
```

## Common Action Patterns

### Edit Button

```
Action Type: Show Form Screen
Configuration: Edit form for current row
Label: "Edit"
```

### Delete with Confirmation

```
Action Type: Delete Row
Configuration: Target current row
Options: Enable confirmation dialog
Label: "Delete"
```

### Mark as Complete

```
Action Type: Set Values
Column: status
Value: "Complete"
Label: "Mark Complete" or "✓ Done"
```

### Send Email

```
Action Type: Compose Email
To: {email column}
Subject: "Regarding: {item name}"
Body: Template text
Label: "Send Email" or "Contact"
```

### Navigate to Related

```
Action Type: Show New Screen
Source: Related item (via relation column)
Label: "View Project" or "See Details"
```

## Error Recovery

### Action Type Not Available

```
ISSUE: Desired action not in list

CHECK:
  1. Some actions are plan-specific (Business/Enterprise)
  2. Some actions only available in certain contexts
  3. Check for similar action with different name
```

### Action Not Triggering

```
ISSUE: Button exists but action doesn't work

CHECK:
  1. Is action configuration complete?
  2. Is there a validation error?
  3. For data actions: is target column writeable?
  4. Test in published app (preview may differ)
```

### Wrong Button Order

```
ISSUE: Buttons appear in wrong order

FIX: Drag actions in list to reorder
     First action = leftmost button
```

### Set Values Not Saving

```
ISSUE: Set Values action doesn't update data

CHECK:
  1. Correct column selected?
  2. Value is valid for column type?
  3. User has write permission?
  4. Is it a computed column? (can't write to computed)
```

## Report Format

```
ACTION ADDED:
  Component: {buttonName or componentName}
  Screen: {screenName}
  Action Type: {actionType}
  Configuration: {key settings}
  Label: {button label if applicable}
  Status: SUCCESS | FAILED
```
