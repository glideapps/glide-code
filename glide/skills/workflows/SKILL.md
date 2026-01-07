---
name: workflows
description: |
  Glide workflows (automations) - triggers, nodes, and automation patterns.
  Covers both client-side App Interactions and server-side workflows.
---

# Glide Workflows Skill

## Overview

Glide workflows automate actions in response to triggers. There are two categories:

| Category | Runs On | Trigger Source | Example |
|----------|---------|----------------|---------|
| **App Interactions** | User's device | User action in app | Button click → navigate |
| **Server-Side Workflows** | Glide servers | External events or schedule | Webhook → add row |

## Workflow Types

### 1. App Interactions (Client-Side)

**Trigger**: User action (button click, screen open, form submit)
**Location**: Created via Layout Editor → Component → Actions

Key characteristics:
- Execute immediately on user action
- Run on the user's device
- Limited to actions that can happen client-side
- Can trigger server-side workflows via "Trigger Workflow" action

Common App Interaction actions:
- Navigate to screen
- Show/hide component
- Set column value
- Open URL
- Send email (via device)
- Trigger Workflow (bridges to server-side)

### 2. Schedule Workflows (Server-Side)

**Trigger**: Time-based schedule
**Location**: Workflows tab

Schedule options:
| Frequency | Configuration |
|-----------|---------------|
| Every 5/15/30 minutes | Automatic |
| Every hour | Automatic |
| Every day | Select days + time |
| Every week | Select day + time |
| Every month | Select date (1-31) + time |

Use cases:
- Daily report generation
- Periodic data sync
- Scheduled notifications
- Cleanup/maintenance tasks

### 3. Webhook Workflows (Server-Side)

**Trigger**: HTTP POST request to unique URL
**Location**: Workflows tab

Receives:
- **Request body** - JSON payload
- **Request headers** - HTTP headers
- **Query parameters** - URL query string

Use cases:
- External system integration (Shopify, Stripe, Zapier)
- API endpoint for third parties
- IoT device data ingestion

### 4. Manual Workflows (Server-Side)

**Trigger**: App Interaction using "Trigger Workflow" action
**Location**: Workflows tab

Features:
- Define input variables
- Variables passed from app screen
- Bridge between client action and server processing

Use cases:
- Complex operations that need server resources
- Operations requiring loops/conditions
- Background processing after user action

### 5. Email Workflows (Server-Side)

**Trigger**: Email received at Glide-provided address
**Location**: Workflows tab

Available variables:
- From, To, Subject, Body
- HTML Body, Plain Text Body
- Attachments (as URLs)
- Date received

Use cases:
- Email-to-database logging
- Support ticket creation
- Lead capture from form submissions
- Email parsing and automation

### 6. Slack Workflows (Server-Side)

**Trigger**: Slack event
**Location**: Workflows tab
**Prerequisite**: Slack integration enabled

Available variables:
- Channel ID/Name
- User ID/Name
- Message text
- Thread info
- Event type

Use cases:
- Message logging
- Slack-to-Glide data sync
- Bot responses
- Team notifications

## Server-Side Exclusive Features

### Loops

Iterate over table rows in server-side workflows.

```
Loop: Over "Orders" table
  Filter: status = "Pending"
  
  For each row:
    → Update Row (set processed = true)
    → Send Email (order confirmation)
```

**Best practices:**
- Always filter loops (never process entire table)
- Be mindful of execution time
- Consider rate limits on external APIs

### Conditions (Branches)

Create branching logic paths.

```
Condition:
  Path 1: IF amount > 1000 → Require approval
  Path 2: IF amount > 100  → Auto-approve, notify manager
  Path 3: ELSE             → Auto-approve
```

**Key rule**: Evaluated **left-to-right**, first match wins.

Put most specific conditions first, general conditions last.

## Common Workflow Nodes

### Data Nodes

| Node | Description |
|------|-------------|
| Add Row | Create new row in table |
| Update Row | Modify existing row |
| Delete Row | Remove row |
| Query JSON | Parse JSON with JSONata |
| Query Table | Find rows matching criteria |

### Communication Nodes

| Node | Description |
|------|-------------|
| Send Email | Send email via Glide |
| Send SMS | Send text message |
| Push Notification | Send to app users |
| Send Slack Message | Post to Slack channel |

### Integration Nodes

| Node | Description |
|------|-------------|
| HTTP Request | Call external API |
| Trigger Workflow | Call another workflow |
| Google Sheets | Interact with Sheets |

### Logic Nodes

| Node | Description |
|------|-------------|
| Condition | Branch based on criteria |
| Loop | Iterate over rows |
| Wait | Pause execution |
| Set Variable | Create/modify variable |

### AI Nodes

| Node | Description |
|------|-------------|
| Generate Text | AI text generation |
| Classify Text | AI categorization |
| Extract Data | AI data extraction |

## JSONata Reference

For parsing JSON in Query JSON nodes:

### Basic Syntax

```jsonata
name                    // Simple field
customer.email          // Nested field
items[0]                // First array item
items[-1]               // Last array item
items[*].name           // All names from array
```

### Common Functions

```jsonata
$count(items)           // Array length
$sum(items.price)       // Sum values
$average(scores)        // Average
$string(number)         // To string
$number(string)         // To number
$now()                  // Current time
$lowercase(text)        // Lowercase
$trim(text)             // Remove whitespace
```

### Filtering

```jsonata
items[price > 100]              // Items over $100
items[status = "active"]        // Active only
orders[total > 0].id            // IDs of non-zero orders
```

## Workflow Patterns

### Pattern: Webhook → Database

```
Trigger: Webhook (receives order data)
  ↓
Query JSON: Extract orderId, customerEmail, total
  ↓
Add Row: Create order in Orders table
  ↓
Send Email: Order confirmation to customer
```

### Pattern: Scheduled Report

```
Trigger: Schedule (daily at 9am)
  ↓
Query Table: Get yesterday's orders
  ↓
Loop: Calculate totals
  ↓
Generate Text (AI): Create summary
  ↓
Send Email: Report to managers
```

### Pattern: Approval Flow

```
Trigger: Manual (from app button)
  ↓
Condition:
  Path 1: amount > 10000
    → Add Row: Create approval request
    → Send Email: Notify approvers
  Path 2: else
    → Update Row: Set approved = true
    → Send Email: Confirmation
```

### Pattern: External API Sync

```
Trigger: Schedule (every hour)
  ↓
HTTP Request: GET external API
  ↓
Query JSON: Parse response
  ↓
Loop: Over items in response
  ↓
  Condition:
    → IF exists in Glide: Update Row
    → ELSE: Add Row
```

### Pattern: Email to Ticket

```
Trigger: On Email
  ↓
Add Row: Create ticket
  - Subject → Title
  - Body → Description
  - From → Requester
  - Status = "New"
  ↓
Send Email: Auto-reply to sender
  ↓
Send Slack: Notify support channel
```

## Error Handling

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| Workflow not triggering | Not enabled | Enable in properties |
| Webhook returns error | Invalid payload | Check JSONata queries |
| Loop timeout | Too many rows | Add filters |
| Email not sending | Invalid address | Validate email format |
| Slack failing | Bot not in channel | Invite bot to channel |

### Debugging Strategies

1. **Check run history** - View past executions and errors
2. **Add logging nodes** - Insert "Add Row" to log table
3. **Test incrementally** - Build and test one node at a time
4. **Validate data** - Use Query JSON to inspect payloads

## Best Practices

### General

1. **Name workflows clearly** - "Daily Sales Report" not "Workflow 1"
2. **Document complex logic** - Add notes explaining branches
3. **Test before deploying** - Use test data first
4. **Monitor executions** - Check run history regularly

### Performance

1. **Filter loops aggressively** - Never iterate entire tables
2. **Batch external calls** - Minimize API requests
3. **Use conditions early** - Short-circuit unnecessary processing
4. **Consider timing** - Schedule heavy jobs during low-usage hours

### Security

1. **Don't expose secrets** - Use environment variables
2. **Validate webhook data** - Check for expected structure
3. **Limit webhook access** - Consider authentication headers
4. **Be careful with loops** - Avoid infinite loops

## Integration with App

### From App to Workflow

1. Create Manual workflow with input variables
2. Add Button to screen
3. Add "Trigger Workflow" action
4. Map screen data to input variables

### From Workflow to App

1. Workflow updates database (Add/Update Row)
2. App displays updated data
3. Use Push Notification for immediate alerts

## Workflow Limits

- Execution time limits (varies by plan)
- API call limits for external requests
- Row processing limits in loops
- Email/SMS sending limits

Check Glide documentation for current limits on your plan.
