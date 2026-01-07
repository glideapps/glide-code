---
name: set-app-settings
description: Configure app name, icon, and appearance settings
preconditions:
  - App exists
---

# Set App Settings Procedure

## Overview

Configures the app's basic settings: name, icon, accent color, and layout style.

## Procedure

### Step 1: Navigate to Settings

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/settings

EXPECTED: Settings page loads
VERIFY: Shows sections like "Name & Icon", "Appearance", "Access"
```

### Step 2: Set App Name

```
COMMAND: SNAPSHOT
PURPOSE: Find Name & Icon section

FIND: "Name & Icon" section
      Input field for app name
```

```
COMMAND: CLICK
REF: {ref of name input}
ELEMENT: App name input
```

```
COMMAND: PRESS_KEY
KEY: Meta+A (select all existing text)
```

```
COMMAND: TYPE
REF: {ref of name input}
ELEMENT: App name input
TEXT: {appName}

RULES:
  - Maximum 2 words
  - No company name (app lives in company's team)
  - No punctuation at end
  - Good: "Inventory", "Task Tracker", "Team Directory"
  - Bad: "Acme Corp Inventory System!", "My Task Tracker."
```

Name saves automatically.

### Step 3: Skip App Icon

**⚠️ DO NOT attempt to change the app icon.**

The emoji picker causes the page state to exceed token limits (170k+ characters). Leave the default icon.

If the user specifically asks for a custom icon later, they can:
1. Upload a custom image manually
2. Or change it themselves in Settings

For now, proceed to the next step.

### Step 4: Set Accent Color (Optional)

```
COMMAND: SNAPSHOT
PURPOSE: Find Appearance section

FIND: "Appearance" section
      Color picker or color options
```

```
COMMAND: CLICK
REF: {ref of color picker/option}
ELEMENT: Accent color selector

EXPECTED: Color options appear
```

If color picker input:
```
COMMAND: TYPE
REF: {ref of color input}
ELEMENT: Color input
TEXT: {hexColor}

EXAMPLE: #3B82F6 (blue), #10B981 (green), #F59E0B (amber)
```

If color swatches:
```
COMMAND: CLICK
REF: {ref of desired color swatch}
ELEMENT: {color name} color option
```

### Step 5: Set Layout Style (Optional)

```
COMMAND: SNAPSHOT
PURPOSE: Find layout options

FIND: "Layout" setting with options:
      - "Left" (sidebar navigation on left)
      - "Top" (navigation at top)
```

```
COMMAND: CLICK
REF: {ref of desired layout option}
ELEMENT: "{Left|Top}" layout option

EXPECTED: Layout style updated
```

### Step 6: Set Theme Style (Optional)

```
COMMAND: SNAPSHOT
PURPOSE: Find theme/style options

FIND: Style options like:
      - "Light" (light background)
      - "Deep" (dark/deep colors)
      - "Accent" (uses accent color prominently)
```

```
COMMAND: CLICK
REF: {ref of desired style}
ELEMENT: "{style}" theme option

EXPECTED: Theme style updated
```

### Step 7: Verify Settings

```
COMMAND: SNAPSHOT
PURPOSE: Confirm all settings applied

VERIFY:
  - App name shows correctly
  - App icon is the selected emoji
  - Colors/theme look right in preview
```

## Error Recovery

### Icon Click Opens File Upload

```
ISSUE: Clicking icon area opens file upload instead of emoji picker

FIX: Click the icon IMAGE itself, not the "Upload" button/text
     The clickable emoji is usually smaller and shows current icon
```

### Emoji Picker Won't Open

```
TRY:
  1. Press Escape to close any overlays
  2. Click directly on the current emoji/icon
  3. Refresh page and retry
```

### Color Not Applying

```
CHECK:
  1. Is the hex code valid? (#RRGGBB format)
  2. Did you press Enter after typing?
  
TRY: Use preset color swatches instead of custom hex
```

### Settings Not Saving

```
NOTE: Most settings auto-save in Glide

IF not saving:
  1. Try clicking outside the input field
  2. Refresh and check if changes persisted
  3. May need to wait a moment for sync
```

## Report Format

```
APP SETTINGS CONFIGURED:
  Name: {appName}
  Icon: {emoji}
  Accent Color: {color}
  Layout: {Left | Top}
  Theme: {Light | Deep | Accent}
  Status: SUCCESS | PARTIAL | FAILED
  Issues: {any problems}
```
