---
name: glide
description: Start working with Glide - includes interactive login, app selection, and task choice
---

# Glide Start Command

This command guides you through the initial setup for working with Glide apps, including authentication and app selection.

**IMPORTANT: Read the main `glide` skill first** - it contains the workflow, agent coordination, and parallel building strategy needed to understand app building.

## Workflow

### Step 0: Read the Glide Skill

**Before doing anything else, read the main `glide` skill.**

This skill covers:
- Overall build workflow and phases
- Parallel building strategy (how to use 6 browsers effectively)
- Agent responsibilities
- Key patterns and rules
- Browser automation details

This context is essential for understanding how to orchestrate the setup and build process.

### Step 1: Check for existing authentication

Check `.glide/config.json` to see if the user is already authenticated.

If the file exists with authentication data or an authenticated browser profile exists:
- Skip to Step 3 (app selection)
- Use the stored authentication for subsequent operations

### Step 2: Interactive Login to Glide

Guide the user through logging in interactively:

1. **Open browser**: Use `mcp__plugin_glide_browser-1__browser_navigate` to go to `https://go.glideapps.com`

2. **Check login status**: Use `mcp__plugin_glide_browser-1__browser_snapshot` to see current state

3. **If already logged in**: (shows "New app" button, dashboard visible)
   - Skip to profile sync

4. **If not logged in**: Guide the user:
   > "Please log in to Glide in the browser window. Click 'Sign In' and complete the authentication. I'll detect when you're done."

5. **Wait for completion**: Poll `mcp__plugin_glide_browser-1__browser_snapshot` every 3 seconds
   - Success: Page shows "New app" button, app list, or team selector (no login form visible)
   - Timeout: After 2 minutes, check if they need help

6. **Sync authenticated profile to all browsers**:
   ```bash
   mkdir -p .glide
   for i in 2 3 4 5 6; do
     rm -rf ".glide/browser-profile-$i"
     cp -R ".glide/browser-profile-1" ".glide/browser-profile-$i"
   done
   ```

7. **Inform user**:
   > "✓ Logged in! All 6 browsers are authenticated and ready for parallel work."

**Store authentication:**
- Create/update `.glide/config.json` to store authenticated status

### Step 3: Choose app (new or existing)

Use AskUserQuestion:
- Question: "Do you want to create a new Glide app or work with an existing one?"
- Options:
  - "Create new app" - Start fresh with a blank Glide app
  - "Edit existing app" - Continue working on an app you've already created

### Step 4: If editing existing, get app details

If the user selected "Edit existing app":

Use AskUserQuestion:
- Question: "Please provide the app URL or app name"
- This will allow "Other" input where they can paste the URL or type the app name

The URL format is typically: `go.glideapps.com/app/{appId}/layout`

Extract the `appId` from the URL if provided, or search by app name.

**Store the app context:**
- Update `.glide/config.json` to include `appUrl` and `appId`

### Step 5: Ask what they want to do

Use AskUserQuestion:
- Question: "What would you like to do with this Glide app?"
- Options:
  - "Build screens and UI" - Use the builder agent
  - "Analyze a file for import" - Import data from a file
  - "Review and improve design" - Use design-review agent
  - "Test and verify features" - Use QA agent
  - "Research existing app" - Explore the app structure

Based on their selection:
- **Build screens**: Launch the `build` agent
- **Analyze file**: Ask for file path, then launch `file-analysis` agent
- **Review design**: Launch the `design-review` agent
- **Test features**: Launch the `qa` agent
- **Research app**: Launch the `app-research` agent

## Implementation Notes

- Step 0 requires reading the main `glide` skill - this is mandatory before proceeding
- Read `.glide/config.json` first to check existing auth and settings
- Handle browser profile copying correctly to support multi-browser work
- Pass app context (appUrl, appId) to all launched agents
- API key is requested by the `data` agent when actually creating tables (not at setup time)
