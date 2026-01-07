---
name: create-email-workflow
description: Create an email workflow triggered by incoming emails
preconditions:
  - App exists
  - Browser authenticated
---

# Create Email Workflow Procedure

## Overview

Creates a server-side workflow triggered when an email is received at a Glide-provided email address. All parts of the email become available as variables.

## Available Variables

| Variable | Description | Example |
|----------|-------------|---------|
| **From** | Sender email address | `john@example.com` |
| **From Name** | Sender display name | `John Doe` |
| **To** | Recipient address (the Glide address) | `abc123@workflows.glideapps.com` |
| **Subject** | Email subject line | `New Order #12345` |
| **Body** | Plain text body | `Order details...` |
| **HTML Body** | HTML formatted body | `<p>Order details...</p>` |
| **Date** | When email was received | Timestamp |
| **Attachments** | File attachments | URLs to attachments |
| **Reply-To** | Reply address if different | `support@example.com` |
| **CC** | CC recipients | Email addresses |

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

### Step 3: Select Email Type

```
COMMAND: SNAPSHOT
PURPOSE: Find workflow type options
```

```
COMMAND: CLICK
REF: {ref of Email option}
ELEMENT: "On Email" or "Email" trigger type

EXPECTED: New email workflow created
VERIFY: Right panel shows email address for receiving
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

EXAMPLES: "Email Lead Capture", "Support Ticket Creator", "Order Parser"
```

### Step 5: Copy Receiving Email Address

```
COMMAND: SNAPSHOT
PURPOSE: Find email address in properties panel

FIND:
  - Email address displayed in right panel
  - Copy button or selectable text
  - Usually format: {unique-id}@workflows.glideapps.com
```

**Copy the email address:**
```
COMMAND: CLICK
REF: {ref of copy button or email field}
ELEMENT: Copy email address button
```

Or select and copy manually.

### Step 6: Extract Email Address

```
COMMAND: EVALUATE
CODE: async () => {
  try {
    const email = await navigator.clipboard.readText();
    return { inboundEmail: email };
  } catch (err) {
    return { error: err.message };
  }
}

EXPECTED: JSON with inboundEmail
SAVE: Store this address to provide to user/external system
```

### Step 7: Add Workflow Nodes

Now add nodes to process the incoming email.

**Common patterns:**

#### Create Support Ticket from Email
1. Add Row to "Tickets" table
   - Title = Subject
   - Description = Body
   - Requester = From
   - Status = "New"

#### Parse Order from Email
1. Query JSON on Body (if structured)
2. Add Row to "Orders" table

#### Forward to Team
1. Send Email node
   - To = team distribution email
   - Subject = "Fwd: " + Subject
   - Body = Body

### Step 8: Using Email Variables in Nodes

When configuring nodes, email variables are available as data sources:

```
COMMAND: SNAPSHOT
PURPOSE: Find data binding in node configuration

FIND: Data/variable selector in node properties
```

```
COMMAND: CLICK
REF: {ref of data selector}
ELEMENT: Data source selector
```

```
COMMAND: SNAPSHOT
PURPOSE: Find email variables

FIND: Email trigger variables:
  - From
  - Subject
  - Body
  - (all others listed above)
```

```
COMMAND: CLICK
REF: {ref of desired variable}
ELEMENT: "{variableName}" email variable

EXPECTED: Node now uses that email data
```

### Step 9: Verify Configuration

```
COMMAND: SNAPSHOT
PURPOSE: Confirm email workflow setup

VERIFY:
  - Workflow name is set
  - Email address is visible and copied
  - Nodes are configured with email variables
```

## Use Cases

### Lead Capture
- Receive: Contact form submissions forwarded via email
- Parse: Name, email, message from subject/body
- Action: Create row in Leads table, notify sales team

### Support Tickets
- Receive: Customer support emails
- Parse: Subject as title, body as description
- Action: Create ticket, send auto-reply

### Email-to-Table
- Receive: Reports or updates from external systems
- Parse: Extract key data from body
- Action: Log to appropriate table

### Notification Relay
- Receive: Alerts from external monitoring
- Parse: Alert type, severity
- Action: Push notification to admins, log event

## Parsing Email Content

### If Body Contains Structured Data

Use Query JSON or string parsing:

**Example - Email body with key:value pairs:**
```
Order ID: 12345
Customer: John Doe
Total: $99.99
```

Use Template columns or string operations in Glide to extract.

### If Body is Natural Text

Consider using AI-powered parsing:
1. Add AI node (Generate Text)
2. Prompt: "Extract the order number from this text: {Body}"
3. Use result in subsequent nodes

## Error Recovery

### Email Address Not Appearing

```
ISSUE: No email address in properties panel

CHECK:
  - Is this an Email type workflow?
  - Is the workflow saved?

TRY: Refresh page and check again
```

### Workflow Not Triggering on Email

```
ISSUE: Sent email but workflow didn't run

CHECK:
  - Is the email sent to exact address? (case matters)
  - Is the sender's email on a blocklist?
  - Did the email get marked as spam?

TRY: Send test email from different sender
```

### Attachments Not Accessible

```
ISSUE: Can't access email attachments

CHECK:
  - Does the email have attachments?
  - Are attachments within size limits?

NOTE: Attachments are provided as URLs, not direct files
```

## Report Format

```
EMAIL WORKFLOW CREATED:
  Name: {workflowName}
  Inbound Email: {email address}
  Variables Used: {list of email variables bound in nodes}
  Nodes Added: {list}
  Status: SUCCESS | FAILED
  
USER ACTION REQUIRED:
  Send emails to: {inbound email address}
  Or configure external system to forward to this address.
```
