---
name: create-app
description: Create a new blank Glide app
preconditions:
  - Browser is authenticated to Glide
---

# Create App Procedure

## Overview

Creates a new blank Glide app. Always use Blank template - never import files.

## Procedure

### Step 1: Navigate to Dashboard

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com

EXPECTED: Glide dashboard loads
VERIFY: Shows app gallery, "New app" button visible
```

### Step 2: Check Authentication

```
COMMAND: SNAPSHOT
PURPOSE: Verify logged in

CHECK: 
  - If "Sign In" button visible → User needs to log in manually
  - If "New app" button visible → Proceed to Step 3
```

If not logged in:
```
REPORT: BLOCKED
REASON: User must log in to Glide
ACTION: Prompt user to sign in, then retry
```

### Step 3: Click New App

```
COMMAND: SNAPSHOT
PURPOSE: Find New App button

FIND: "New app" button or card
      Usually prominent on dashboard
      May show as "+ New app" or just "New app"
```

```
COMMAND: CLICK
REF: {ref of New app button}
ELEMENT: "New app" button

EXPECTED: App template selection modal appears
```

### Step 4: Select Blank Template

```
COMMAND: SNAPSHOT
PURPOSE: Find template options

FIND: Template selection UI showing options like:
      - "Blank" (what we want)
      - "Import a file" (NEVER use this)
      - Various templates
```

```
COMMAND: CLICK
REF: {ref of Blank option}
ELEMENT: "Blank" template option

EXPECTED: Blank option is selected/highlighted
```

### Step 5: Create the App

```
COMMAND: SNAPSHOT
PURPOSE: Find Create button

FIND: "Create app" or "Create" button
      Usually at bottom of modal
```

```
COMMAND: CLICK
REF: {ref of Create button}
ELEMENT: "Create app" button

EXPECTED: Modal closes, new app loads in editor
WAIT: May take 2-5 seconds for app to initialize
```

### Step 6: Wait for Editor to Load

```
COMMAND: WAIT_FOR_TEXT
TEXT: Layout

EXPECTED: Layout tab appears, indicating editor is ready

ALTERNATIVE TEXT: "Data" or "Settings" - any main nav tab
```

```
COMMAND: SNAPSHOT
PURPOSE: Confirm app loaded

VERIFY:
  - Main editor UI visible
  - Navigation tabs (Data, Layout, Settings) present
  - Preview area shows empty app
```

### Step 7: Extract App ID

```
COMMAND: EVALUATE
CODE: () => window.location.href

EXPECTED: URL like "https://go.glideapps.com/app/{APP_ID}/layout"
```

Parse the APP_ID from the URL for future operations.

### Step 8: Set App Name

Set the app name (skip the emoji icon - it causes UI issues):

```
COMMAND: CLICK
REF: {ref of Settings tab}
ELEMENT: "Settings" tab in main navigation

EXPECTED: Settings page loads
```

```
COMMAND: SNAPSHOT
PURPOSE: Find name input

FIND: "Name" or "Title" input field in Settings
      Usually in "Name & Icon" section at top
```

```
COMMAND: CLICK
REF: {ref of name input}
ELEMENT: App name input
```

```
COMMAND: PRESS_KEY
KEY: Meta+A
PURPOSE: Select all existing text
```

```
COMMAND: TYPE
REF: {ref of name input}
ELEMENT: App name input
TEXT: {appName}

RULES:
  - 2 words maximum
  - No company name
  - No punctuation
  - Good: "Tasks", "Inventory", "CRM", "Team"
  - Bad: "Acme Task Manager!", "My Company's Inventory System"
```

App name saves automatically - no save button needed.

**⚠️ SKIP THE EMOJI PICKER** - Don't try to change the app icon. The emoji picker causes the page state to exceed token limits. Leave the default icon.

## Verification

```
COMMAND: SNAPSHOT
PURPOSE: Final verification

VERIFY:
  - App exists and is editable
  - Can navigate between Data, Layout, Settings tabs
  - App name shows correctly (if set)
```

## Error Recovery

### "New app" Button Not Found

```
ISSUE: Dashboard doesn't show new app option

CHECK:
  1. Are you on the correct URL? (go.glideapps.com)
  2. Is the page fully loaded?
  3. Are you logged in to the correct team?

TRY: Refresh page, or navigate directly to go.glideapps.com
```

### Template Modal Doesn't Appear

```
ISSUE: Clicking New app doesn't open modal

CHECK:
  1. Is there a popup blocker?
  2. Is another modal already open?

TRY:
  COMMAND: PRESS_KEY
  KEY: Escape
  
  Then retry clicking New app
```

### App Creation Fails

```
ISSUE: Create button doesn't work or errors

CHECK:
  1. Is there an error message displayed?
  2. Is the account over app limit?
  3. Network connectivity issues?

TRY: Refresh and retry, or check Glide status
```

### Editor Doesn't Load After Creation

```
ISSUE: Stuck on loading state

WAIT: Up to 10 seconds (new apps can be slow)

IF still stuck:
  COMMAND: NAVIGATE
  URL: https://go.glideapps.com
  
  Find the new app in dashboard and click to open
```

## Important Notes

1. **NEVER use "Import a file"** - Always use Blank and add data via API
2. **App names must be short** - Enforce 2 word maximum
3. **Save the App ID** - Needed for all future operations
4. **New apps have a Users table** - This is normal and expected

## Report Format

```
APP CREATED:
  Name: {appName}
  App ID: {appId}
  URL: https://go.glideapps.com/app/{appId}/layout
  Status: SUCCESS | FAILED
  Default Tables: Users
```
