---
description: Audit Glide app for performance issues - analyze data structure and layout for bottlenecks
argument-hint: [glide-app-url]
---

# Glide App Performance Audit

You are performing a comprehensive performance audit of a Glide application.

## Task

Analyze the provided Glide app for performance issues and optimization opportunities.

**App URL**: $ARGUMENTS

## What to Audit

### Data Layer Analysis
- Deep computed column chains (6+ layers → Critical)
- Relation proliferation (5+ relations → Warning)
- Rollups without relations (Critical)
- Heavy AI column usage (3+ → Warning)
- Query column overuse (2+ → Warning)
- Row limit issues (80%+ → Warning)

### Layout Analysis
- Collection field density (10+ fields → Optimization)
- Inline collection overload (3+ → Warning)
- Component overload (20+ → Warning, 30+ → Critical)
- Unoptimized table views (AI columns visible → Warning)

## Workflow

Follow the procedures in `glide/skills/app-audit/`:

1. **Fetch App Data** (`procedures/fetch-app-data.md`)
   - Parse app URL to extract App ID
   - Navigate to app Data Editor
   - Extract API token via browser automation
   - Fetch tables and schemas via API (GET requests only)

2. **Analyze Data Structure** (`procedures/analyze-data-structure.md`)
   - Build column dependency graphs
   - Detect performance anti-patterns
   - Calculate severity scores
   - Identify critical issues

3. **Analyze Layout** (`procedures/analyze-layout.md`)
   - Navigate to Layout Editor
   - Inspect screens and components
   - Count collection fields and inline collections
   - Flag layout anti-patterns

4. **Generate Report** (`procedures/generate-report.md`)
   - Compile findings by severity
   - Calculate performance score (0-100)
   - Generate markdown report with:
     - Executive summary
     - Critical issues (🔴)
     - Warnings (🟡)
     - Optimizations (🟢)
     - Performance score breakdown
     - Priority actions

## Important Safety Notes

⚠️ **READ-ONLY OPERATIONS ONLY**
- Only use browser navigation and snapshots (READ operations)
- Only use API GET requests (NO POST/PATCH/PUT/DELETE)
- NEVER click Save, Update, or Confirm buttons
- NEVER modify columns, components, or settings
- If you accidentally open an edit dialog, press ESC to close

The app must remain exactly as it was before the audit.

## Reference Documentation

- **[Analysis Patterns](glide/skills/app-audit/analysis-patterns.md)** - Detailed anti-pattern descriptions
- **[Recommendations](glide/skills/app-audit/recommendations.md)** - Fix strategies for each issue type
- **[Main Skill](glide/skills/app-audit/SKILL.md)** - Full audit documentation

## Output

Generate a comprehensive markdown report with:
- Overall health status and score
- Issues grouped by severity
- Specific locations (table names, screen names)
- Performance impact explanations
- Actionable recommendations with steps to fix
- Priority action list

Display the report to the user after analysis is complete.
