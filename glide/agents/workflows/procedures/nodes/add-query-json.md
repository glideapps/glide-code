---
name: add-query-json-node
description: Add a Query JSON node to parse JSON data with JSONata
preconditions:
  - Workflow exists
  - Have JSON data to parse (webhook body, API response, etc.)
---

# Add Query JSON Node Procedure

## Overview

Adds a Query JSON node to extract specific values from JSON data using JSONata syntax. Essential for webhook workflows that receive JSON payloads.

## When to Use

| Source | Use Query JSON For |
|--------|-------------------|
| **Webhook body** | Parse incoming API payloads |
| **HTTP Response** | Extract data from API calls |
| **Email body** | Parse structured email content |
| **Any JSON** | Navigate nested objects |

## JSONata Quick Reference

| Need | JSONata Syntax | Input | Output |
|------|---------------|-------|--------|
| Simple field | `fieldName` | `{"name": "John"}` | `"John"` |
| Nested field | `customer.name` | `{"customer": {"name": "John"}}` | `"John"` |
| Array first item | `items[0]` | `{"items": ["a", "b"]}` | `"a"` |
| Array count | `$count(items)` | `{"items": [1, 2, 3]}` | `3` |
| Array sum | `$sum(items.price)` | `{"items": [{price: 10}, {price: 20}]}` | `30` |

## Procedure

### Step 1: Ensure in Workflow Editor

```
COMMAND: SNAPSHOT
PURPOSE: Verify in workflow canvas

VERIFY:
  - Workflow editor is open
  - Trigger node exists (typically Webhook for JSON parsing)
```

### Step 2: Add Query JSON Node

```
COMMAND: SNAPSHOT
PURPOSE: Find add node option

FIND:
  - "+" button on canvas
  - Connection from trigger or previous node
```

```
COMMAND: CLICK
REF: {ref of add node button}
ELEMENT: Add node button (+)

EXPECTED: Node picker appears
```

### Step 3: Find Query JSON in Picker

```
COMMAND: SNAPSHOT
PURPOSE: Find Query JSON node

FIND:
  - Search input
  - "Data" category
  - "Query JSON" or "Parse JSON" option
```

**Using search:**
```
COMMAND: TYPE
REF: {ref of search input}
ELEMENT: Node search
TEXT: query json

EXPECTED: Query JSON node appears
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of Query JSON node}
ELEMENT: "Query JSON" node option

EXPECTED: Query JSON node added to canvas
```

### Step 4: Select JSON Source

```
COMMAND: SNAPSHOT
PURPOSE: Find source selector in node config

FIND:
  - "Source" or "Data" dropdown in right panel
  - Input field for JSON source
```

```
COMMAND: CLICK
REF: {ref of source dropdown}
ELEMENT: JSON source selector
```

```
COMMAND: SNAPSHOT
PURPOSE: Find available sources
```

For Webhook workflows:
```
COMMAND: CLICK
REF: {ref of request body option}
ELEMENT: "The request body"

EXPECTED: Source set to incoming webhook payload
```

For HTTP Response:
```
COMMAND: CLICK
REF: {ref of http response option}
ELEMENT: HTTP Response body or previous node output
```

### Step 5: Enter JSONata Query

```
COMMAND: SNAPSHOT
PURPOSE: Find JSONata query input

FIND:
  - Query input field
  - May be labeled "Query", "Expression", or "JSONata"
  - Usually below the source selector
```

```
COMMAND: CLICK
REF: {ref of query input}
ELEMENT: JSONata query input
```

```
COMMAND: TYPE
REF: {ref of query input}
ELEMENT: JSONata expression
TEXT: {jsonataQuery}

EXAMPLES:
  orderId
  customer.email
  items[0].name
  $count(lineItems)
  $sum(items.quantity)
```

### Step 6: Name the Output Variable

```
COMMAND: SNAPSHOT
PURPOSE: Find output name field

FIND:
  - "Name" or "Output" or "Variable name" field
  - This is how you'll reference this value later
```

```
COMMAND: TYPE
REF: {ref of name input}
ELEMENT: Output variable name
TEXT: {variableName}

EXAMPLES:
  orderId
  customerEmail
  itemCount
  
RULES:
  - Use camelCase
  - Be descriptive
  - Match the data it contains
```

### Step 7: Add More Query JSON Nodes

For each piece of data you need to extract, add another Query JSON node.

**Example for order payload:**
```json
{
  "id": 12345,
  "customer": {
    "name": "John Doe",
    "email": "john@example.com"
  },
  "items": [
    {"sku": "ABC", "qty": 2, "price": 29.99}
  ],
  "total": 59.98
}
```

**Nodes to add:**

| Node # | Source | Query | Output Name |
|--------|--------|-------|-------------|
| 1 | Request body | `id` | `orderId` |
| 2 | Request body | `customer.name` | `customerName` |
| 3 | Request body | `customer.email` | `customerEmail` |
| 4 | Request body | `total` | `orderTotal` |
| 5 | Request body | `$count(items)` | `itemCount` |

### Step 8: Use Extracted Values

After Query JSON nodes, the extracted values are available as variables.

In subsequent nodes (Add Row, Send Email, etc.):

```
COMMAND: SNAPSHOT
PURPOSE: Find data binding in another node
```

```
COMMAND: CLICK
REF: {ref of data selector}
ELEMENT: Data source
```

```
COMMAND: SNAPSHOT
PURPOSE: Find Query JSON outputs

FIND:
  - Variables from Query JSON nodes
  - Named by the output variable names you set
```

```
COMMAND: CLICK
REF: {ref of query output variable}
ELEMENT: "{variableName}" from Query JSON

EXPECTED: Node uses the extracted JSON value
```

### Step 9: Verify Configuration

```
COMMAND: SNAPSHOT
PURPOSE: Confirm Query JSON setup

VERIFY:
  - All needed data points have Query JSON nodes
  - Sources are correct (request body, etc.)
  - JSONata queries are valid
  - Output names are descriptive
```

## JSONata Syntax Guide

### Basic Access

| Pattern | Example | Description |
|---------|---------|-------------|
| `field` | `name` | Top-level field |
| `parent.child` | `customer.email` | Nested field |
| `arr[0]` | `items[0]` | First array element |
| `arr[-1]` | `items[-1]` | Last array element |
| `arr[*].field` | `items[*].name` | All 'name' from array |

### Functions

| Function | Example | Result |
|----------|---------|--------|
| `$count()` | `$count(items)` | Number of items |
| `$sum()` | `$sum(items.price)` | Total of prices |
| `$average()` | `$average(scores)` | Average value |
| `$string()` | `$string(id)` | Convert to string |
| `$number()` | `$number(qty)` | Convert to number |
| `$now()` | `$now()` | Current timestamp |
| `$lowercase()` | `$lowercase(name)` | Lowercase string |
| `$uppercase()` | `$uppercase(code)` | Uppercase string |
| `$trim()` | `$trim(input)` | Remove whitespace |

### Conditional

| Pattern | Example | Description |
|---------|---------|-------------|
| Ternary | `status = "active" ? "Yes" : "No"` | If-else inline |
| Default | `name ? name : "Unknown"` | Fallback if null |

### Filtering Arrays

```jsonata
items[price > 100]          # Items over $100
items[status = "active"]    # Active items only
items[qty > 0].name         # Names of in-stock items
```

## Common Patterns

### Webhook from Shopify

```json
{"order": {"id": 123, "customer": {"email": "..."}}}
```

Queries:
- `order.id`
- `order.customer.email`

### Webhook from Stripe

```json
{"data": {"object": {"id": "...", "amount": 9999}}}
```

Queries:
- `data.object.id`
- `data.object.amount`

### Webhook from Zapier

```json
{"fields": {"Name": "John", "Email": "john@..."}}
```

Queries:
- `fields.Name`
- `fields.Email`

## Error Recovery

### Query Returns Empty

```
ISSUE: Query JSON output is empty/null

CHECK:
  - Is the JSONata path correct?
  - Does the field exist in the payload?
  - Is there a typo in the field name?

DEBUG:
  - Log the raw request body to see actual structure
  - Try a simpler query like just `$` to see entire payload
```

### Invalid JSONata Syntax

```
ISSUE: Error in query expression

CHECK:
  - Field names should not have quotes for simple access
  - Function names start with $
  - Array indices use square brackets

EXAMPLES of common fixes:
  Wrong: "customer.name"
  Right: customer.name
  
  Wrong: count(items)
  Right: $count(items)
```

### Nested Path Not Working

```
ISSUE: Nested access returns nothing

CHECK:
  - Is the parent object null?
  - Are you using . for objects and [] for arrays?

EXAMPLE:
  If items is an array: items[0].name
  If items is an object: items.name
```

## Report Format

```
QUERY JSON NODE(S) ADDED:
  Workflow: {workflowName}
  Source: {request body | HTTP response | etc.}
  Queries:
    - {variableName}: {jsonataQuery}
    - {variableName}: {jsonataQuery}
  Status: SUCCESS | FAILED
  Notes: {any issues}
```
