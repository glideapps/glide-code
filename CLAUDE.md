# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **Claude Code plugin** for building Glide applications. The plugin enables Claude Code to create and modify Glide apps using browser automation (Playwright) and the Glide API v2.

## Project Structure

```
glide/                     # The Claude Code plugin
  .claude-plugin/
    plugin.json            # Plugin manifest
  .mcp.json                # MCP server config (Playwright)
  agents/                  # Autonomous agents for specific tasks
  commands/                # Slash commands (/start, /tip)
  skills/                  # Knowledge and instructions for various domains
  CLAUDE.md                # Plugin-specific guidelines (read by agents)
```

## Key Components

**Agents** - Autonomous workers launched via the Task tool:
- `build` - Browser automation for creating apps and screens
- `data` - Glide API operations for tables and data import
- `file-analysis` - Analyzes spreadsheets to extract data models
- `design-review` - Critiques screens for design quality
- `qa` - Verifies features work before declaring app ready
- `app-research` - Explores existing apps to document structure
- `skill-learner` - Extracts learnings from tips and updates skills

**Skills** - Domain knowledge in `skills/{name}/SKILL.md`:
- `glide` - Primary skill: workflow, agent coordination, builder navigation
- `design` - Screen structure, components, layouts
- `data-modeling` - Tables, columns, Big Tables
- `computed-columns` - Math, If-Then-Else, Relations, Rollups
- `api` - Glide API v2 usage
- `app-sharing` - Privacy, authentication, publishing

**Commands** - User-invocable slash commands:
- `/start` - Guided setup workflow
- `/tip` - Provide expert guidance, plugin learns and updates skills

## Plugin Architecture

The plugin uses Playwright MCP for browser automation. Agents read skill files for domain knowledge and use Playwright tools (`browser_navigate`, `browser_snapshot`, `browser_click`, `browser_type`, etc.) to interact with the Glide Builder at go.glideapps.com.

Build workflow: Create blank app → Analyze file (if provided) → Create tables via API → Build screens via browser → Design review → QA verification → Finalize

## Editing Guidelines

When modifying this plugin:
- Agent files use YAML frontmatter for `name`, `description`, and `skills`
- Skill files use YAML frontmatter for `name` and `description`
- Command files use YAML frontmatter for `name`, `description`, and other metadata
- The `glide/CLAUDE.md` contains guidelines read by agents during execution
- Skills should be self-contained and reference other skills by name when needed
