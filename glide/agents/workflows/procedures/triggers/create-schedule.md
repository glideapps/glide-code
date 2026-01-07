---
name: create-schedule-workflow
description: Create a scheduled workflow that runs on a time-based trigger
preconditions:
  - App exists
  - Browser authenticated
---

# Create Schedule Workflow Procedure

## Overview

Creates a server-side workflow that runs automatically on a schedule. Options include every X minutes, hourly, daily, weekly, or monthly.

## Schedule Options

| Frequency | Configuration |
|-----------|---------------|
| Every 5 minutes | No additional config |
| Every 15 minutes | No additional config |
| Every 30 minutes | No additional config |
| Every hour | No additional config |
| Every day | Select days of week + time |
| Every week | Select day of week + time |
| Every month | Select day of month (1-31) + time |

## Procedure

### Step 1: Navigate to Workflows

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/workflows

EXPECTED: Workflow editor loads
VERIFY: Shows workflow list or empty state with "New workflow" option
```

### Step 2: Create New Workflow

```
COMMAND: SNAPSHOT
PURPOSE: Find new workflow button

FIND: "New workflow" or "+" button
      May be in header or center of empty state
```

```
COMMAND: CLICK
REF: {ref of new workflow button}
ELEMENT: New workflow button

EXPECTED: Workflow type selection appears
```

### Step 3: Select Schedule Type

```
COMMAND: SNAPSHOT
PURPOSE: Find workflow type options

FIND: List of trigger types:
      - Schedule
      - Webhook
      - Manual
      - On Email
      - Slack
```

```
COMMAND: CLICK
REF: {ref of Schedule option}
ELEMENT: "Schedule" trigger type

EXPECTED: New schedule workflow created
VERIFY: Right panel shows schedule configuration
```

### Step 4: Name the Workflow

```
COMMAND: SNAPSHOT
PURPOSE: Find workflow name input

FIND: Name field in right properties panel
      May be at top of panel or in header
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

EXAMPLES: "Daily Report", "Hourly Sync", "Weekly Cleanup"
```

### Step 5: Configure Schedule Frequency

```
COMMAND: SNAPSHOT
PURPOSE: Find frequency selector

FIND: Frequency dropdown or options:
      - Every 5 minutes
      - Every 15 minutes
      - Every 30 minutes
      - Every hour
      - Every day
      - Every week
      - Every month
```

```
COMMAND: CLICK
REF: {ref of frequency dropdown}
ELEMENT: Schedule frequency selector
```

```
COMMAND: SNAPSHOT
PURPOSE: Find desired frequency option
```

```
COMMAND: CLICK
REF: {ref of desired frequency}
ELEMENT: "{frequency}" option

EXPECTED: Frequency selected
```

### Step 6: Configure Time/Day Settings

Configuration varies by frequency:

#### For "Every day":

```
COMMAND: SNAPSHOT
PURPOSE: Find day and time selectors

FIND:
  - Day checkboxes (Mon, Tue, Wed, Thu, Fri, Sat, Sun)
  - Time picker
```

**Select days:**
```
COMMAND: CLICK
REF: {ref of day checkbox}
ELEMENT: "{dayName}" checkbox

REPEAT: For each day the workflow should run
```

**Set time:**
```
COMMAND: CLICK
REF: {ref of time picker}
ELEMENT: Time picker
```

```
COMMAND: TYPE or SELECT
REF: {ref of time input}
ELEMENT: Time input
TEXT: {time}

EXAMPLE: "09:00", "14:30"
```

#### For "Every week":

```
COMMAND: SNAPSHOT
PURPOSE: Find day and time selectors

FIND:
  - Day selector (single day of week)
  - Time picker
```

```
COMMAND: CLICK
REF: {ref of day selector}
ELEMENT: Day of week selector
```

```
COMMAND: CLICK
REF: {ref of desired day}
ELEMENT: "{dayName}" option
```

Set time as above.

#### For "Every month":

```
COMMAND: SNAPSHOT
PURPOSE: Find date and time selectors

FIND:
  - Day of month selector (1-31)
  - Time picker
```

```
COMMAND: CLICK
REF: {ref of date selector}
ELEMENT: Day of month selector
```

```
COMMAND: TYPE or SELECT
REF: {ref of date input}
ELEMENT: Day of month
TEXT: {dayNumber}

EXAMPLE: "1" (first of month), "15" (mid-month)
```

Set time as above.

### Step 7: Enable the Schedule

```
COMMAND: SNAPSHOT
PURPOSE: Find enable toggle

FIND: Toggle or switch to enable/activate the schedule
      May say "Enabled", "Active", or similar
```

```
COMMAND: CLICK
REF: {ref of enable toggle}
ELEMENT: Enable schedule toggle

EXPECTED: Schedule is now active
VERIFY: Toggle shows "on" state
```

### Step 8: Verify Configuration

```
COMMAND: SNAPSHOT
PURPOSE: Confirm schedule settings

VERIFY:
  - Workflow name is set
  - Frequency is correct
  - Days/time are configured
  - Schedule is enabled
```

## Next Steps

After creating the trigger, add nodes to define what the workflow does:

1. Add data operations (query, add, update rows)
2. Add communication (send email, notification)
3. Add conditions for logic
4. Add loops for batch processing

See `nodes/` procedures for adding workflow nodes.

## Error Recovery

### Schedule Not Saving

```
ISSUE: Changes don't persist

CHECK:
  - Is there a save button? (Some workflows auto-save)
  - Are all required fields filled?
  - Is there an error message?

TRY: Click outside the field to trigger save
```

### Time Picker Not Working

```
ISSUE: Can't set time

CHECK:
  - Is it a dropdown or text input?
  - Does it need 24-hour format?

TRY: Type time directly: "09:00" or "9:00 AM"
```

### Schedule Not Running

```
ISSUE: Workflow created but not executing

CHECK:
  - Is the schedule enabled?
  - Is the time zone correct?
  - Does the workflow have nodes to execute?

NOTE: Schedule won't run if there are no actions defined
```

## Report Format

```
SCHEDULE WORKFLOW CREATED:
  Name: {workflowName}
  Frequency: {every X minutes | daily | weekly | monthly}
  Schedule: {specific times/days}
  Enabled: {yes | no}
  Status: SUCCESS | FAILED
  Notes: {any issues}
```
