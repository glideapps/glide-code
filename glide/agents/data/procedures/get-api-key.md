---
name: get-api-key
description: Retrieve API key from Glide app for data operations
preconditions:
  - Browser is authenticated to Glide
  - App exists
---

# Get API Key Procedure

## Overview

Retrieves the API token from a Glide app. This token is needed for all API operations (creating tables, adding rows, etc.).

## When to Use

- Before any `data-agent` API operations
- When setting up a new app
- If stored API key has expired or is invalid

## Procedure

### Step 1: Navigate to Data Editor

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/data

EXPECTED: Data Editor loads
VERIFY: "Data" tab is active, table list visible
```

### Step 2: Select Any Table

We need a table selected to access the API panel.

```
COMMAND: SNAPSHOT
PURPOSE: Find a table to select

FIND: Any table in the left sidebar
      "Users" table always exists in new apps
```

```
COMMAND: CLICK
REF: {ref of table, e.g., Users}
ELEMENT: "{tableName}" table

EXPECTED: Table opens in main area
```

### Step 3: Open API Panel

```
COMMAND: SNAPSHOT
PURPOSE: Find "Show API" button

FIND: "Show API" button
      Usually in the toolbar above the table
      Or in a menu/dropdown
```

```
COMMAND: CLICK
REF: {ref of Show API button}
ELEMENT: "Show API" button

EXPECTED: API panel/modal appears
VERIFY: Shows code examples and token
```

### Step 4: Locate the Token

```
COMMAND: SNAPSHOT
PURPOSE: Find the API token in the panel

FIND: The panel typically shows:
      1. Code examples (curl, JavaScript)
      2. A "secret token" or API key
      3. May have a "Copy" button next to it

LOOK FOR:
  - Text starting with "Bearer " followed by a long string
  - A masked/hidden token with a "Show" or "Copy" button
  - An input field containing the token
```

### Step 5: Extract/Copy Token

**If there's a copy button:**

```
COMMAND: CLICK
REF: {ref of copy button near token}
ELEMENT: "Copy" or "Copy token" button

EXPECTED: Token copied to clipboard
NOTE: May need to read from clipboard or find token in UI
```

**If token is visible in text:**

```
COMMAND: SNAPSHOT
PURPOSE: Read the token value

FIND: The actual token string
      Usually looks like: a long alphanumeric string
      Format varies but typically 30+ characters
```

**If token is in a code example:**

Look for pattern:
```
Authorization: Bearer {TOKEN_HERE}
```
or
```
-H "Authorization: Bearer {TOKEN_HERE}"
```

Extract the token portion.

### Step 6: Use Token

Once you have the token:

1. **Keep it in memory** for the current session
2. **Pass it to API calls** with `Authorization: Bearer {token}`
3. Token is now available for all data operations

### Step 7: Close API Panel

```
COMMAND: PRESS_KEY
KEY: Escape

Or:

COMMAND: CLICK
REF: {ref of close button}
ELEMENT: Close button on API panel
```

## Verification

Test the token works:

```bash
curl -X GET "https://api.glideapps.com/tables" \
  -H "Authorization: Bearer {TOKEN}"
```

Should return list of tables (or empty list for new app).

## Error Recovery

### "Show API" Button Not Found

```
ISSUE: Can't find the Show API button

CHECK:
  1. Is a table selected? (button may only appear when table is open)
  2. Look in different locations:
     - Top toolbar
     - Table header area
     - Right-click menu on table
     - "..." overflow menu

TRY: Select a different table, look again
```

### Token Appears Masked

```
ISSUE: Token shows as asterisks or dots

FIND: "Show" or "Reveal" button near the token
CLICK: The reveal button
THEN: Token should become visible
```

### API Panel Won't Open

```
ISSUE: Nothing happens when clicking Show API

TRY:
  1. Refresh the page
  2. Navigate directly to Data Editor
  3. Try a different table
  4. Check if there's a popup blocker
```

### Token Doesn't Work (401 Error)

```
ISSUE: API calls return 401 Unauthorized

CHECK:
  1. Token copied correctly? (no extra spaces)
  2. Token from correct app?
  3. Is this a Big Table? (API v2 only works with Big Tables)

TRY: Get a fresh token and try again
```

## Important Notes

1. **API v2 only works with Big Tables** - Native tables can't be accessed via API
2. **Token is per-team, not per-app** - Same token works for all Big Tables in the team
3. **Keep token secure** - Don't commit to version control
4. **Token may expire** - If API calls start failing, get a fresh token

## Report Format

```
API KEY RETRIEVED:
  App ID: {appId}
  Token: {first 8 chars}...{last 4 chars} (masked for security)
  Status: SUCCESS | FAILED
  Verified: Yes | No
```
