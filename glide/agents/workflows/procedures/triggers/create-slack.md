---
name: create-slack-workflow
description: Create a Slack workflow triggered by Slack events
preconditions:
  - App exists
  - Browser authenticated
  - Slack integration linked to Glide team
  - Glide app added to Slack workspace
---

# Create Slack Workflow Procedure

## Overview

Creates a server-side workflow triggered by events in Slack. Requires Slack integration to be enabled and the Glide bot added to the Slack workspace.

## Prerequisites

Before creating a Slack workflow:

1. **Slack Integration Enabled** - In Glide team settings
2. **Glide Bot in Slack** - Added to workspace
3. **Bot in Channel** - Invited to channels it needs to monitor

If not set up, report to user:
```
BLOCKED: Slack integration required

USER ACTION NEEDED:
1. Go to Team Settings → Integrations
2. Connect Slack workspace
3. Add Glide bot to Slack workspace
4. Invite bot to relevant channels
```

## Available Variables

Slack workflows receive event data as variables:

| Variable | Description | Example |
|----------|-------------|---------|
| **Channel ID** | Slack channel identifier | `C1234567890` |
| **Channel Name** | Human-readable channel name | `#general` |
| **User ID** | Slack user identifier | `U1234567890` |
| **User Name** | User's display name | `john.doe` |
| **Message Text** | Content of the message | `Hello team!` |
| **Timestamp** | When event occurred | Slack timestamp |
| **Thread TS** | Thread timestamp (if reply) | Thread identifier |
| **Event Type** | Type of Slack event | `message`, `reaction_added` |

*Available variables depend on the specific event type configured.*

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

### Step 3: Select Slack Type

```
COMMAND: SNAPSHOT
PURPOSE: Find workflow type options
```

```
COMMAND: CLICK
REF: {ref of Slack option}
ELEMENT: "Slack" trigger type

EXPECTED: New Slack workflow created
VERIFY: Right panel shows Slack configuration options
```

### Step 4: Check Integration Status

```
COMMAND: SNAPSHOT
PURPOSE: Verify Slack integration

FIND: Slack connection status in properties panel
      May show "Connected" or "Connect Slack" button

IF not connected:
  REPORT: BLOCKED
  REASON: Slack integration not linked
  ACTION: User must connect Slack in Team Settings
```

### Step 5: Name the Workflow

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

EXAMPLES: "Slack Message Logger", "Support Channel Bot", "Feedback Collector"
```

### Step 6: Configure Slack Event

```
COMMAND: SNAPSHOT
PURPOSE: Find Slack event configuration

FIND: Event type selector or configuration options:
  - Which channels to monitor
  - Which event types to trigger on
  - Filter conditions
```

**Select event type (if available):**
```
COMMAND: CLICK
REF: {ref of event type dropdown}
ELEMENT: Slack event type selector
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of event option}
ELEMENT: "{eventType}" option

EVENT TYPES may include:
  - Message posted
  - Reaction added
  - File shared
  - Channel joined
  - etc.
```

**Select channel(s) (if available):**
```
COMMAND: CLICK
REF: {ref of channel selector}
ELEMENT: Channel selector
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of channel}
ELEMENT: "#{channelName}" channel

NOTE: Bot must be in the channel to receive events
```

### Step 7: Add Workflow Nodes

Now add nodes to process the Slack event.

**Common patterns:**

#### Log Messages to Table
1. Add Row to "Messages" table
   - Channel = Channel Name
   - User = User Name
   - Content = Message Text
   - Timestamp = Timestamp

#### Auto-Reply to Keywords
1. Condition: Check if Message Text contains "help"
2. If true: Send Slack message back to channel

#### Create Task from Message
1. Add Row to "Tasks" table
   - Title = Message Text
   - Source = "Slack"
   - Reporter = User Name

### Step 8: Using Slack Variables in Nodes

```
COMMAND: SNAPSHOT
PURPOSE: Find data binding in node configuration
```

```
COMMAND: CLICK
REF: {ref of data selector}
ELEMENT: Data source selector
```

```
COMMAND: SNAPSHOT
PURPOSE: Find Slack variables

FIND: Slack trigger variables:
  - Channel ID
  - Channel Name
  - User ID
  - User Name
  - Message Text
  - etc.
```

```
COMMAND: CLICK
REF: {ref of desired variable}
ELEMENT: "{variableName}" Slack variable

EXPECTED: Node now uses that Slack data
```

### Step 9: Verify Configuration

```
COMMAND: SNAPSHOT
PURPOSE: Confirm Slack workflow setup

VERIFY:
  - Workflow name is set
  - Slack integration connected
  - Event type/channels configured
  - Nodes use Slack variables correctly
```

## Use Cases

### Message Logger
- Event: Any message in specific channel
- Action: Log to table for analytics

### Keyword Responder
- Event: Message containing specific keywords
- Action: Post helpful response or escalate

### Feedback Collector
- Event: Messages in #feedback channel
- Action: Create row in Feedback table, notify product team

### Support Bot
- Event: Messages mentioning @support
- Action: Create support ticket, acknowledge in thread

## Sending Messages Back to Slack

To respond in Slack:

1. Add "Send Slack Message" node
2. Configure:
   - Channel: Use Channel ID from trigger (or hardcode)
   - Message: Your response text
   - Thread: Use Thread TS to reply in thread

## Error Recovery

### Slack Not Connected

```
ISSUE: "Connect Slack" showing, no integration

USER ACTION REQUIRED:
1. Go to go.glideapps.com/settings (Team Settings)
2. Find Integrations section
3. Click Connect Slack
4. Authorize Glide bot in Slack workspace
```

### Channel Not Listed

```
ISSUE: Can't find channel in selector

CHECK:
  - Is the Glide bot invited to that channel?
  - Is it a private channel? (bot needs explicit invite)

USER ACTION: Invite @Glide bot to the channel in Slack
```

### Workflow Not Triggering

```
ISSUE: Sent Slack message but workflow didn't run

CHECK:
  - Is workflow enabled?
  - Is bot in the correct channel?
  - Does event type match? (message vs reaction, etc.)
  - Is there a filter excluding this event?
```

### Can't Send Slack Messages

```
ISSUE: Send Slack Message node fails

CHECK:
  - Does bot have permission to post in that channel?
  - Is Channel ID correct?
  - Is message content valid?
```

## Report Format

```
SLACK WORKFLOW CREATED:
  Name: {workflowName}
  Integration Status: Connected
  Event Type: {message | reaction | etc.}
  Channels: {list of channels or "all"}
  Variables Used: {list}
  Nodes Added: {list}
  Status: SUCCESS | BLOCKED | FAILED
  
NOTES: {any issues or user instructions}
```
