# Glide Plugin - Important Guidelines

**New to this plugin?** Run `/start` to get set up - it will guide you through choosing new/existing app, storing your API key, and selecting what to do.

**Got stuck?** Run `/tip` to provide expert guidance - the plugin will learn from your tip and update its skills for future use.

See the `glide` skill for detailed workflow and builder documentation. **Read it first when building any Glide app.**

## Build Workflow Summary

1. **Clarify use case** - Confirm what the user wants, or ask if unclear
2. **Create app first** - Get browser auth done early (don't make user wait during file analysis)
3. **Analyze file** (if provided) - Use `file-analysis` agent with context hints
4. **Create tables via API** - Use `data` agent (faster than UI, always create NEW tables)
5. **Build basic screens** - Use `build` agent for Playwright automation
6. **Design review** - Use `design-review` agent to critique and improve screens (collection styles, components, data density)
7. **QA verification** - Use `qa` agent to verify features actually work before telling user it's ready
8. **Finalize** - Access settings, testing, publish

## Files → file-analysis Agent

**ALWAYS use `file-analysis` first** when user provides a spreadsheet, CSV, or data file.

Pass context hints:
- What kind of app they want
- Key features mentioned
- Who will use it

The file-analysis agent returns:
- Data to import (tables, columns)
- Relationships (→ relation columns)
- Formulas to rebuild (→ computed columns)

## Agent Responsibilities

- **build**: Creates app first (browser auth), then builds via Playwright
- **file-analysis**: Analyzes files, creates specifications (data, relationships, formulas)
- **data**: Creates tables and imports data via Glide API
- **design-review**: Critiques screens for design quality on mobile and desktop, pushes for better component choices and layouts
- **qa**: Verifies features actually work before telling user the app is ready
- **app-research**: Explores existing apps to document structure, features, and data model (comprehensive or focused)
- **skill-learner**: Extracts learnings from expert tips and updates skill files (called automatically by `/tip` command)

## Key Rules

1. **Always create Blank apps** - NEVER use "Import a file" option
2. **Create app first** - Get browser auth done early, don't make user wait
3. **Files → file-analysis agent** - Don't skip this step; pass context hints
4. **Create NEW tables** - Don't inject data into existing tables
5. **Use API for data import** - Don't use Glide's UI file upload
6. **Always include images** - Use picsum.photos placeholders
7. **Don't use the Agent tab** in Glide Builder - it's error-prone
