---
name: start
description: Start working with Glide - choose to create new app or edit existing one
---

# Glide Start Command

This command guides you through the initial setup for working with Glide apps.

## Workflow

### Step 0: Check for existing API key

Before starting, read `.claude/glide.local.md` to see if an API key is already stored.

If the file exists and contains an API key in the YAML frontmatter:
- Skip Step 3 (asking for API key)
- Use the stored API key for subsequent operations

### Step 1: Ask if creating new or editing existing

Use AskUserQuestion to ask:
- Question: "Do you want to create a new Glide app or edit an existing one?"
- Options:
  - "Create new app" - Start fresh with a blank Glide app
  - "Edit existing app" - Continue working on an app you've already created

### Step 2: If editing existing, get app details

If the user selected "Edit existing app":

Use AskUserQuestion to ask:
- Question: "Please provide either the app URL or app name"
- This will allow "Other" input where they can paste the URL or type the app name

The URL format is typically: `go.glideapps.com/app/{appId}/layout`

Extract the `appId` from the URL if provided, or use the app name to search for it.

**Store the app context:**
- Update `.claude/glide.local.md` to include the `appUrl` and `appId`
- This helps agents know which app to work on

### Step 3: Get and store API key

Ask for the Glide API key:

Use AskUserQuestion:
- Question: "Please provide your Glide API key (you can get this from your Glide account settings)"
- This will allow "Other" input for the API key

**Store the API key securely:**

1. Create/update `.claude/glide.local.md` with YAML frontmatter
2. This file is in .gitignore (excluded from version control)
3. Format:
   ```markdown
   ---
   apiKey: "user's-api-key-here"
   appUrl: ""
   appId: ""
   ---

   # Glide Configuration

   This file stores your Glide API key and current app context locally.
   It is excluded from version control for security.

   **How to get your API key:**
   1. Open any app in Glide Builder (go.glideapps.com)
   2. Go to Data tab
   3. Click "Show API" button
   4. Click "Copy secret token"
   ```

4. After storing, also set the environment variable: `GLIDE_API_TOKEN={apiKey}`

### Step 4: Ask what they want to do

Use AskUserQuestion:
- Question: "What would you like to do with this Glide app?"
- Options:
  - "Build screens and UI" - Use the builder agent for UI work
  - "Import or manage data" - Use the data agent for tables and data
  - "Analyze a file for import" - Use file-analysis agent
  - "Review and improve design" - Use design agent
  - "Test and verify features" - Use QA agent
  - "Research existing app" - Use app-research agent to explore the app

Based on their selection:
- **Build screens**: Launch the `builder` agent
- **Import data**: Launch the `data` agent
- **Analyze file**: Ask for file path, then launch `file-analysis` agent
- **Review design**: Launch the `design` agent
- **Test features**: Launch the `qa` agent
- **Research app**: Launch the `app-research` agent

## Implementation Notes

- Always read `.claude/glide.local.md` first to check if API key already exists
- If API key exists, skip asking for it (unless user wants to update it)
- Store the current app URL/ID in the conversation context for agents to use
- Pass the app context to whichever agent is launched
