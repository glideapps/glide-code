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

```typescript
mcp__playwright__browser_navigate({
  url: "https://go.glideapps.com"
})

// Expected Result:
// - Glide dashboard loads
// - App gallery visible
// - "New app" button present
```

### Step 2: Check Authentication

```typescript
const snapshot = mcp__playwright__browser_snapshot()

// Check authentication status:
// - If "Sign In" button exists → User needs to log in manually
// - If "New app" button exists → Authenticated, proceed to Step 3

if (snapshot contains "Sign In") {
  // Not authenticated
  return {
    status: "BLOCKED",
    reason: "User must log in to Glide",
    action: "Please sign in to Glide in the browser, then I'll continue"
  }
}
```

**If not logged in:** Wait for user to authenticate manually, then retry from Step 1.

### Step 3: Click New App

```typescript
// Take snapshot to find the button
const snapshot = mcp__playwright__browser_snapshot()
// Extract ref for "New app" button (usually prominent on dashboard)
const newAppButtonRef = find(snapshot, "New app")

mcp__playwright__browser_click({
  ref: newAppButtonRef,
  element: "New app button"
})

// Verification: Take another snapshot to confirm modal opened
const verification = mcp__playwright__browser_snapshot()
// Assert: Template selection modal is visible with "Blank" option

if (!verification.contains("Blank")) {
  // Retry logic: Modal didn't appear
  wait(1000)
  retry click (max 2 retries)
}
```

### Step 4: Select Blank Template

```typescript
const templateSnapshot = mcp__playwright__browser_snapshot()
const blankRef = find(templateSnapshot, "Blank")

mcp__playwright__browser_click({
  ref: blankRef,
  element: "Blank template option"
})

// Verification: Blank option should be selected/highlighted
```

**IMPORTANT:** Never select "Import a file" - always use Blank.

### Step 5: Create the App

```typescript
const createSnapshot = mcp__playwright__browser_snapshot()
const createButtonRef = find(createSnapshot, "Create app")

mcp__playwright__browser_click({
  ref: createButtonRef,
  element: "Create app button"
})

// Expected: Modal closes, app creation begins
// Note: May take 2-5 seconds for app to initialize
```

### Step 6: Wait for Editor to Load

```typescript
mcp__playwright__browser_wait_for({
  text: "Layout",
  // Timeout after 10 seconds
})

// Verify editor loaded properly
const editorSnapshot = mcp__playwright__browser_snapshot()
// Assert: Main editor UI visible
// Assert: Navigation tabs (Data, Layout, Settings) present
// Assert: Preview area shows empty app
```

### Step 7: Extract App ID

```typescript
const currentUrl = mcp__playwright__browser_evaluate({
  function: "() => window.location.href"
})

// Parse APP_ID from URL format:
// "https://go.glideapps.com/app/{APP_ID}/layout"
const appId = extractFromUrl(currentUrl, /app\/([^\/]+)/)

// Store appId for all future operations
```

### Step 8: Set App Name

```typescript
// Navigate to Settings tab
const settingsSnapshot = mcp__playwright__browser_snapshot()
const settingsTabRef = find(settingsSnapshot, "Settings")

mcp__playwright__browser_click({
  ref: settingsTabRef,
  element: "Settings tab"
})

// Wait for settings to load
wait(500)

// Find and click name input
const nameSnapshot = mcp__playwright__browser_snapshot()
const nameInputRef = find(nameSnapshot, "Name")

mcp__playwright__browser_click({
  ref: nameInputRef,
  element: "App name input"
})

// Select all existing text
mcp__playwright__browser_press_key({ key: "Meta+A" })

// Type new app name
mcp__playwright__browser_type({
  ref: nameInputRef,
  element: "App name input",
  text: appName
})

// App name saves automatically
```

**App Naming Rules:**
- 2 words maximum
- No company name
- No punctuation
- ✅ Good: "Tasks", "Inventory", "CRM"
- ❌ Bad: "Acme Task Manager!", "My Company's Inventory System"

**⚠️ SKIP THE EMOJI PICKER** - Don't change the app icon. The emoji picker causes token overflow. Leave the default icon.

## Verification

```typescript
const finalSnapshot = mcp__playwright__browser_snapshot()

// Verify all requirements met:
assert(finalSnapshot.contains("Data"))
assert(finalSnapshot.contains("Layout"))
assert(finalSnapshot.contains("Settings"))
assert(appNameIsSet === true)
assert(appId !== null)

// Success criteria:
// ✅ App exists and is editable
// ✅ Can navigate between Data, Layout, Settings tabs
// ✅ App name shows correctly
// ✅ App ID extracted from URL
```

## Error Recovery Matrix

| Error | Detection | Recovery Strategy | Max Retries |
|-------|-----------|------------------|-------------|
| **"New app" button not found** | Snapshot doesn't contain "New app" | 1. Check URL is go.glideapps.com<br>2. Wait 2s for page load<br>3. Refresh page<br>4. Navigate to go.glideapps.com again | 3 |
| **Template modal doesn't appear** | After click, snapshot doesn't contain "Blank" | 1. Press Escape key<br>2. Wait 500ms<br>3. Retry click on "New app" | 2 |
| **Create button doesn't work** | Modal still open after click | 1. Check for error message in snapshot<br>2. Verify "Blank" is selected<br>3. Wait 1s and retry click | 2 |
| **Editor doesn't load** | No "Layout" text after 10s | 1. Wait additional 5s<br>2. Navigate to go.glideapps.com<br>3. Find new app in dashboard and click | 1 |
| **App ID extraction fails** | URL doesn't match pattern | 1. Check current URL format<br>2. Navigate to /layout route<br>3. Retry extraction | 2 |
| **Name input not found** | Settings page doesn't show name field | 1. Wait 1s for settings to load<br>2. Refresh snapshot<br>3. Try alternate selector for "Title" | 2 |

## Important Notes

1. **NEVER use "Import a file"** - Always use Blank and add data via API
2. **App names must be short** - Enforce 2 word maximum
3. **Save the App ID** - Needed for all future operations
4. **New apps have a Users table** - This is normal and expected

## Report Format

Return structured output:

```json
{
  "status": "SUCCESS",
  "appName": "Inventory",
  "appId": "abc123xyz",
  "appUrl": "https://go.glideapps.com/app/abc123xyz/layout",
  "defaultTables": ["Users"],
  "timestamp": "2024-01-11T10:30:00Z",
  "notes": "App created successfully with default name set"
}
```

**On failure:**
```json
{
  "status": "FAILED",
  "error": "Template modal didn't appear after 2 retries",
  "step": "Step 3: Click New App",
  "recoveryAttempted": ["Pressed Escape", "Retried click"],
  "suggestion": "Check if popup blocker is enabled"
}
```
