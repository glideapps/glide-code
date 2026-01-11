---
name: app-audit
description: |
  Audit Glide apps for performance issues and optimization opportunities.
  Use when analyzing app performance, identifying bottlenecks in data structure or layout,
  or providing recommendations for improving app speed and user experience.
  Automatically triggered when user provides a Glide app URL for audit.
---

# Glide App Audit

Comprehensive performance audit tool for Glide applications. Analyzes data architecture and layout design to identify bottlenecks, anti-patterns, and optimization opportunities.

## ⚠️ Read-Only Guarantee

**This tool is 100% read-only and NEVER modifies your app:**
- ✅ Reads data via API (GET requests only)
- ✅ Navigates through Layout Editor to inspect screens
- ✅ Takes snapshots to analyze components
- ✅ Clicks to view settings panels (read-only)
- ❌ NEVER edits columns, components, or settings
- ❌ NEVER adds or deletes anything
- ❌ NEVER saves any changes
- ❌ NEVER modifies data or structure

**Safe to run anytime** - Your app remains exactly as it was before the audit.

## What It Audits

### Data Layer Analysis
- **Computed column dependencies** - Detects deep chains causing latency
- **Relation structures** - Identifies proliferation and misuse
- **Rollup configurations** - Flags rollups without relations
- **AI column usage** - Tracks expensive AI inference
- **Query columns** - Detects overuse of cross-table queries
- **Row limits** - Warns about tables approaching capacity

### Layout Analysis
- **Collection density** - Checks field counts in cards/lists/tables
- **Inline collections** - Counts nested queries on detail screens
- **Component load** - Identifies screens with too many components
- **Table optimizations** - Detects heavy computed columns in views

## How to Use

### Basic Usage

```
/app-audit https://go.glideapps.com/app/abc123
```

The audit will:
1. Extract app ID from URL
2. Connect to app and retrieve API token
3. Analyze data structure via API
4. Inspect layout via browser automation
5. Generate comprehensive markdown report

### What You'll Get

A detailed markdown report with:
- **Executive summary** - High-level findings and priorities
- **Critical issues** - Problems causing significant performance impact (🔴)
- **Warnings** - Potential issues as app scales (🟡)
- **Optimizations** - Best practice improvements (🟢)
- **Performance score** - Overall health rating (0-100)
- **Priority actions** - Step-by-step remediation plan

Each issue includes:
- Specific location (table name, column name, screen name)
- Performance impact explanation
- Actionable recommendations with steps to fix

## Audit Workflow

### Phase 1: Connect to App
1. Parse Glide app URL to extract App ID
2. Navigate to app in browser (Data Editor)
3. Use browser automation to extract API token
4. Verify API access works

### Phase 2: Data Structure Analysis
1. Fetch all tables via API (`GET /tables`)
2. For each table, get schema with all columns
3. Build column dependency graph
4. Detect performance anti-patterns:
   - Count computed column depth (flag 4+ layers)
   - Count relations per table (flag 5+)
   - Identify rollups on tables vs relations
   - Count AI columns (flag 3+)
   - Count Query columns (flag 2+)
   - Check row counts vs limits

### Phase 3: Layout Analysis
1. Navigate to Layout Editor
2. For each tab/screen:
   - Count total components
   - Identify inline collections
   - Check collection field counts
   - Note heavy components (charts, maps)
3. Flag layout anti-patterns:
   - Card collections with 10+ fields
   - Detail screens with 3+ inline collections
   - Screens with 20+ components

### Phase 4: Generate Report
1. Compile findings by severity (Critical, Warning, Optimization)
2. Calculate performance scores
3. Generate markdown report with:
   - Issue descriptions
   - Specific locations
   - Impact explanations
   - Actionable recommendations
4. Display report to user

## Performance Issues Detected

### Critical (🔴)

| Issue | Detection | Impact |
|-------|-----------|--------|
| **Deep computed column chains** | 6+ layer dependency | High latency on all data operations |
| **Rollup without relation** | Rollup on table, not relation | Processes all rows unnecessarily |
| **Circular dependencies** | Column depends on itself | App errors, infinite loops |
| **Row limit exceeded** | Near or over table limits | Data loss, sync failures |

### Warnings (🟡)

| Issue | Detection | Impact |
|-------|-----------|--------|
| **High relation count** | 5+ relations per table | Increased query load |
| **AI column proliferation** | 3+ AI columns | High computation cost |
| **Query overuse** | 2+ Query columns per table | Slow cross-table lookups |
| **Inline collection overload** | 3+ inline collections per screen | Multiple nested queries |

### Optimizations (🟢)

| Issue | Detection | Impact |
|-------|-----------|--------|
| **Dense collections** | Card showing 10+ fields | Heavy rendering, poor UX |
| **Component overload** | 20+ components per screen | Slow rendering, cluttered UI |
| **Unoptimized table views** | Computed columns in collection | Unnecessary calculations |
| **Moderate column depth** | 4-5 layer chains | Moderate latency |

## Understanding the Report

### Performance Score (0-100)

- **90-100**: Excellent - Well-architected, minimal issues
- **75-89**: Good - Some optimizations available
- **60-74**: Needs Attention - Performance issues present
- **Below 60**: Critical - Significant problems requiring immediate action

Breakdown:
- **Data Architecture** (40 points) - Column design, relations, queries
- **Layout Design** (40 points) - Screen structure, component usage
- **Best Practices** (20 points) - Following Glide patterns

### Issue Severity Guide

**Critical (🔴)** - Fix immediately:
- Causing active performance problems
- Users experiencing slow load times
- Risk of data issues or app errors

**Warning (🟡)** - Address soon:
- Will cause problems as app scales
- Degraded performance under load
- Maintenance difficulties

**Optimization (🟢)** - Improve when possible:
- Best practice violations
- User experience enhancements
- Code maintainability

## Supporting Documentation

For detailed information on specific issues:

- **[Analysis Patterns](analysis-patterns.md)** - Deep dive on each anti-pattern
- **[Recommendations](recommendations.md)** - Fix strategies and best practices

For implementation details:

- **[Procedures](procedures/)** - Browser automation and API scripts
  - `fetch-app-data.md` - Data extraction
  - `analyze-data-structure.md` - Column dependency analysis
  - `analyze-layout.md` - UI inspection
  - `generate-report.md` - Report generation

For Glide reference:

- **[Computed Columns](../computed-columns/SKILL.md)** - Column patterns
- **[Data Modeling](../data-modeling/SKILL.md)** - Table design
- **[Design](../design/SKILL.md)** - Layout best practices

## Limitations

- **100% Read-only** - Does NOT modify app structure, settings, data, or components. Only inspects and analyzes.
- **Big Tables priority** - Best results with Big Tables (API v2 compatible)
- **Layout requires browser** - Some layout analysis needs UI inspection
- **Large apps take time** - Complex apps may need 1-2 minutes to audit
- **API rate limits** - Very large apps may hit rate limits

**Safety Note**: The audit tool uses only GET requests (API) and read-only browser navigation. It never clicks Save, Update, or any modification buttons.

## Example Output

```markdown
# Glide App Audit Report

**App**: Task Manager
**App ID**: abc123def456
**Audit Date**: 2026-01-10
**Overall Health**: 🟡 Needs Attention

## Executive Summary

Your app has moderate performance issues primarily in data architecture.
Key concerns: deep computed column chains in Tasks table (6 layers),
rollups not using relations, and heavy collection field counts.

## Critical Issues (🔴)

### 1. Deep Computed Column Chain
**Location**: `Tasks` table
**Issue**: 6-layer computed column dependency chain
**Columns**: Due Date → Days Until Due → Is Overdue → Status Flag → Status Emoji → Display Status
**Recommendation**: Flatten to 2-3 layers, combine logic with If-Then-Else

### 2. Rollup Without Relation
**Location**: `Projects` table, `Task Count` column
**Issue**: Rollup operating on entire Tasks table
**Recommendation**: Create relation first, then rollup on relation

## Performance Score: 65/100

- Data Architecture: 55/100 (needs improvement)
- Layout Design: 70/100 (good)
- Best Practices: 70/100 (good)

## Priority Actions

1. Fix Tasks table computed column chain
2. Update Project rollups to use relations
3. Reduce inline collections on detail screens
```

## Tips for App Builders

After receiving an audit:

1. **Start with Critical issues** - These have immediate impact
2. **Test incrementally** - Fix one issue, verify improvement
3. **Use browser testing** - Check load times before/after
4. **Consider refactoring** - Sometimes redesign beats patching
5. **Run periodic audits** - Check performance as app evolves

## When to Run an Audit

- **Before launch** - Catch issues early
- **After major changes** - Verify performance impact
- **When users report slowness** - Diagnose bottlenecks
- **Regular maintenance** - Monthly check for growing apps
- **Scaling up** - Before adding significant data volume
