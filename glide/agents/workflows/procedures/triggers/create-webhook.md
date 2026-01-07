---
name: create-webhook-workflow
description: Create a webhook workflow triggered by external HTTP requests
preconditions:
  - App exists
  - Browser authenticated
---

# Create Webhook Workflow Procedure

## Overview

Creates a server-side workflow triggered by HTTP requests to a unique webhook URL. Ideal for integrating with external systems (Zapier, Shopify, custom APIs).

## Available Variables

Webhook workflows have three built-in variables:

| Variable | Description | Use For |
|----------|-------------|---------|
| **The request body** | JSON payload sent in request | Main data payload |
| **The request headers** | HTTP headers | Auth tokens, content type |
| **The request query parameters** | URL query string | `?id=123&action=update` |

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

FIND: "New workflow" or "+" button
```

```
COMMAND: CLICK
REF: {ref of new workflow button}
ELEMENT: New workflow button

EXPECTED: Workflow type selection appears
```

### Step 3: Select Webhook Type

```
COMMAND: SNAPSHOT
PURPOSE: Find workflow type options
```

```
COMMAND: CLICK
REF: {ref of Webhook option}
ELEMENT: "Webhook" trigger type

EXPECTED: New webhook workflow created
VERIFY: Right panel shows webhook URL
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

EXAMPLES: "Shopify Order Import", "Zapier Contact Sync", "API Handler"
```

### Step 5: Copy Webhook URL

```
COMMAND: SNAPSHOT
PURPOSE: Find webhook URL in properties panel

FIND: 
  - URL displayed in right panel
  - Copy button next to URL
  - May be labeled "Webhook URL" or "Endpoint"
```

**Method A: Use copy button**
```
COMMAND: CLICK
REF: {ref of copy button}
ELEMENT: Copy webhook URL button

EXPECTED: URL copied to clipboard
```

**Method B: Select and copy URL**
```
COMMAND: CLICK
REF: {ref of URL field}
ELEMENT: Webhook URL field
```

```
COMMAND: PRESS_KEY
KEY: Meta+A (select all)
```

```
COMMAND: PRESS_KEY
KEY: Meta+C (copy)
```

### Step 6: Extract Webhook URL

```
COMMAND: EVALUATE
CODE: async () => {
  try {
    const url = await navigator.clipboard.readText();
    return { webhookUrl: url };
  } catch (err) {
    return { error: err.message };
  }
}

EXPECTED: JSON with webhookUrl
SAVE: Store this URL to provide to user/external system
```

### Step 7: Add Query JSON Nodes (Recommended)

For webhook workflows, always add Query JSON nodes to parse the incoming data.

```
COMMAND: SNAPSHOT
PURPOSE: Find node canvas and add button

FIND: Workflow canvas with trigger node
      "+" button to add nodes
```

See `nodes/add-query-json.md` for detailed steps.

**Quick Summary:**
1. Click "+" or drag from trigger node
2. Search for "Query JSON" or find in Data category
3. Add node
4. Configure:
   - Source: "The request body"
   - Query: JSONata expression (e.g., `orderId`, `customer.email`)

Repeat for each piece of data you need to extract.

### Step 8: Verify Configuration

```
COMMAND: SNAPSHOT
PURPOSE: Confirm webhook setup

VERIFY:
  - Workflow name is set
  - Webhook URL is visible
  - Query JSON nodes added for data parsing
  - URL has been copied for external use
```

## Parsing Webhook Data

### Example: Shopify Order

Incoming payload:
```json
{
  "id": 12345,
  "customer": {
    "name": "John Doe",
    "email": "john@example.com"
  },
  "total": 99.99,
  "items": [
    {"name": "Widget", "qty": 2}
  ]
}
```

Query JSON nodes to add:

| Node Name | Source | JSONata Query |
|-----------|--------|---------------|
| Order ID | The request body | `id` |
| Customer Name | The request body | `customer.name` |
| Customer Email | The request body | `customer.email` |
| Total | The request body | `total` |
| Item Count | The request body | `$count(items)` |

### Example: Simple Webhook

Incoming payload:
```json
{
  "action": "create",
  "userId": "abc123",
  "data": {
    "name": "New Item"
  }
}
```

Query JSON nodes:

| Node Name | JSONata Query |
|-----------|---------------|
| Action Type | `action` |
| User ID | `userId` |
| Item Name | `data.name` |

## Testing the Webhook

Provide the user with:
1. The webhook URL
2. Expected payload format
3. Test command (if applicable):

```bash
curl -X POST "{webhookUrl}" \
  -H "Content-Type: application/json" \
  -d '{"test": true, "message": "Hello from test"}'
```

## Error Recovery

### Webhook URL Not Appearing

```
ISSUE: No URL in properties panel

CHECK:
  - Is the workflow saved?
  - Is this a Webhook type (not Schedule or Manual)?

TRY: Refresh page and check again
```

### Can't Copy URL

```
ISSUE: Copy fails

TRY:
  1. Click directly in URL field
  2. Select all (Cmd+A)
  3. Copy (Cmd+C)
  4. Or manually select and right-click → Copy
```

### Webhook Not Receiving Data

```
ISSUE: External system says it sent data but workflow didn't run

CHECK:
  - Is the URL correct? (no typos)
  - Is the request method POST?
  - Is Content-Type application/json?
  - Does the workflow have any nodes? (empty workflows may not log)
```

## Report Format

```
WEBHOOK WORKFLOW CREATED:
  Name: {workflowName}
  Webhook URL: {url}
  Query JSON Nodes: {list of fields being parsed}
  Status: SUCCESS | FAILED
  Notes: {any issues or user instructions}

USER ACTION REQUIRED:
  Configure external system to POST to: {webhookUrl}
```
