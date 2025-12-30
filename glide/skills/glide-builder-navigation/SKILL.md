# Glide Builder Navigation

This skill teaches Claude how to navigate and interact with the Glide Builder interface at go.glideapps.com using Playwright browser automation.

## When to Use

Load this skill when users ask to:
- Build an app in Glide
- Navigate the Glide Builder
- Create or modify a Glide app
- Work with the Glide Builder interface

## Quick Reference

### URLs

| URL | Purpose |
|-----|---------|
| `go.glideapps.com` | Dashboard - view/manage apps |
| `go.glideapps.com/app/{appId}/layout` | Layout Editor |
| `go.glideapps.com/app/{appId}/data` | Data Editor |
| `go.glideapps.com/app/{appId}/settings` | App Settings |

### Main Navigation Tabs

The builder has 4 main tabs you'll use:

1. **Data** - Data Editor for tables and columns
2. **Layout** - Screen and component design
3. **Workflows** - Automation sequences
4. **Settings** - App configuration

**Warning**: There is also an "Agent" tab in the builder. **Do not use it.** It's prone to too many errors. Build everything manually using the Data and Layout tabs instead.

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Cmd+K` | Open search |
| `Cmd+Shift+Enter` | Data operations |

## Creating a New App

1. Navigate to `go.glideapps.com`
2. Click "New app" card (first item in apps grid)
3. Select starting point:
   - **Blank** - Empty app (recommended for full control)
   - **Basic App** - Pre-configured starter
   - **Portal** - Portal template
4. Or select data source:
   - Import File, Google Sheets, Airtable, Excel Online
   - Glide Big Tables
   - PostgreSQL, MySQL, Cloud SQL (Enterprise)
5. Click "Create app"

## Data Editor Navigation

### Accessing Data Editor
- Click "Data" tab in top navigation
- URL: `go.glideapps.com/app/{appId}/data`

### Left Sidebar Structure
- **Tables** section: Lists all Glide Tables
- **Data Sources** section: External data connections

### Adding Tables
- Click "+" button next to "Tables" heading
- Or click the collapse/expand button to manage tables

### Adding Columns (Important!)
To add a new column:
1. **Click on any column header** to see context menu
2. Select **"Add column right"** from menu
3. Type the column **Name**
4. **Always explicitly select the Type** from dropdown (even for Text columns)
5. Configure type-specific options
6. Click **Save**

**Playwright Note**: Always click the Type dropdown and select a type explicitly. Interacting with the type picker commits the column name. If you skip this step (leaving default Text), the name may not save.

Alternative: Click the "+" icon at the right end of the column headers row.

### Column Type Selection
When clicking the Type dropdown, you'll see categories:
- **Basic** - Text, Number, Boolean, Image, Date & Time, etc.
- **Computed** - Math, Template, If-Then-Else, Relations, etc.
- **AI** - Audio to Text, Generate Text, Text to JSON, etc.
- **Integrations** - Call API, Claude, OpenAI, Google Maps, etc.

The dropdown is searchable - type to filter column types.

### Table Context Menu
Right-click or click a table in the sidebar for options:
- Rename
- Duplicate
- Delete
- Export

## Layout Editor Navigation

### Accessing Layout Editor
- Click "Layout" tab in top navigation
- URL: `go.glideapps.com/app/{appId}/layout`

### Left Sidebar Structure
- **Navigation** section: Screens shown in main nav bar
- **Menu** section: Screens in the slide-out menu
- **Components** section: Components on current screen

### Adding Screens
1. Click "+" button in Navigation or Menu section
2. Choose screen type:
   - **Screen from data** - Collection screen linked to table
   - **Custom screen** - Blank screen
   - **Form screen** - Data entry form
3. Or select a sample screen template:
   - Project management, Dashboard, Company directory
   - Multi-Step form, Chat

### Screen Configuration Panel
When a screen is selected, the right panel shows:
- **General** tab: Source, Label, Style options
- **Options** tab: Additional configuration

### Style Options for Collection Screens
- Card, List, Table, Data Grid
- Checklist, Calendar, Kanban
- Custom

### Adding Components
1. Select a screen first
2. Click "+" in Components section (left sidebar)
3. Browse or search component categories:
   - AI, Title, Collections, Layout
   - Text, Content, Actions
   - Forms, Form Elements, Advanced
4. Click component to add it

## Settings Navigation

### Accessing Settings
- Click "Settings" tab in top navigation
- URL: `go.glideapps.com/app/{appId}/settings`

### Settings Categories

**Name & Icon**
- App icon (upload or choose)
- Title
- Show: Icon and Title / Icon only / Title only
- Logo upload
- Description

**Appearance**
- Accent Color (8 preset colors)
- Layout: Left / Top
- Style: Light / Deep / Accent
- Environment: Auto / Light / Dark
- Background: Highlight / Neutral
- Content Width: Large / Medium / Small
- Navigation checkboxes
- Custom CSS

**Access**
- Control who can access the app

**Integrations**
- External service connections

**Advanced (under "Advanced" heading)**
- User data
- Sign-In Screen
- Data settings

## Viewing Modes

### Preview Modes
- Click phone/tablet icons to switch preview size
- "Viewing as [User]" dropdown to test as different users

### Publishing
- Click "Publish" button (top right) to publish app
- Manage publishing settings in Settings > Access

## Working with the API Panel

In Data Editor, click "Show API" to see:
- Code samples in JavaScript, Python, curl
- Operations: Get rows, Add rows, Edit rows, Delete rows
- Click "Copy secret token" for API authentication
- Click "Copy code" for ready-to-use snippets

## Tips for Efficient Navigation

1. **Search with Cmd+K** to find anything fast
2. **Column context menu** is faster than the + button for adding columns
3. **Type in the component filter** to quickly find components
4. **Right panel** always shows configuration for selected item
5. **Escape key** closes dialogs and panels
6. **Data first, then Layout** - create tables before building screens

## Documentation

For detailed information, see [Glide Documentation](https://www.glideapps.com/docs).
