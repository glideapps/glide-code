# Generate Report Procedure

Compile analysis results into a comprehensive, actionable markdown audit report.

## Overview

This procedure takes the results from data structure and layout analysis and generates a formatted markdown report with:
- Executive summary
- Issues by severity (Critical, Warning, Optimization)
- Data structure summary
- Performance scores
- Priority actions

## Input

Combined analysis results:

```javascript
{
  appId: "abc123def456",
  appName: "Task Manager",
  auditDate: "2026-01-10T15:30:00Z",

  // From analyze-data-structure
  dataAnalysis: {
    issues: [...],
    tableAnalysis: [...],
    summary: {...},
    scores: { dataArchitecture: 25, bestPractices: 15 }
  },

  // From analyze-layout
  layoutAnalysis: {
    issues: [...],
    screenAnalysis: [...],
    scores: { layoutDesign: 30 }
  }
}
```

## Output

Formatted markdown report ready to display to user.

## Report Structure

### 1. Header Section

```markdown
# Glide App Audit Report

**App**: {appName}
**App ID**: {appId}
**Audit Date**: {formatted date}
**Overall Health**: {health emoji and status}

---
```

**Health Status Calculation**:
```javascript
function determineHealthStatus(overallScore) {
  if (overallScore >= 90) return "🟢 Excellent";
  if (overallScore >= 75) return "🟢 Good";
  if (overallScore >= 60) return "🟡 Needs Attention";
  return "🔴 Critical";
}
```

---

### 2. Executive Summary

High-level findings and key priorities.

```markdown
## Executive Summary

{summary text}

### Key Findings:
- {finding 1}
- {finding 2}
- {finding 3}
```

**Summary Generation**:
```javascript
function generateExecutiveSummary(allIssues, scores) {
  const critical = allIssues.filter(i => i.severity === "critical");
  const warnings = allIssues.filter(i => i.severity === "warning");

  // Identify top concerns
  const topConcerns = [];

  if (critical.length > 0) {
    topConcerns.push(
      `${critical.length} critical ${critical.length === 1 ? "issue" : "issues"} requiring immediate attention`
    );
  }

  // Group by type
  const byType = groupIssuesByType(allIssues);
  const mostCommonType = Object.entries(byType)
    .sort((a, b) => b[1].length - a[1].length)[0];

  if (mostCommonType && mostCommonType[1].length >= 3) {
    topConcerns.push(
      `Multiple ${formatIssueType(mostCommonType[0])} issues detected`
    );
  }

  // Score-based concerns
  if (scores.dataArchitecture < 20) {
    topConcerns.push("Significant data architecture problems");
  }
  if (scores.layoutDesign < 20) {
    topConcerns.push("Major layout optimization needed");
  }

  // Build summary text
  let summary;
  if (scores.overall >= 75) {
    summary = "Your app is in good health with only minor optimizations recommended.";
  } else if (scores.overall >= 60) {
    summary = "Your app has moderate performance issues that should be addressed.";
  } else {
    summary = "Your app has significant performance issues requiring immediate attention.";
  }

  if (topConcerns.length > 0) {
    summary += " Key concerns: " + topConcerns.join(", ") + ".";
  }

  return summary;
}
```

---

### 3. Critical Issues Section

List all critical severity issues with full details.

```markdown
## Critical Issues (🔴)

### 1. {Issue Title}
**Location**: {table/screen name}
**Issue**: {description}
**Impact**: {performance impact}
**Columns/Details**: {specific details}
**Recommendation**:
{step-by-step fix}

---

### 2. {Next Critical Issue}
...
```

**Template**:
```javascript
function formatCriticalIssue(issue, index) {
  return `
### ${index + 1}. ${formatIssueTitle(issue)}
**Location**: \`${issue.location}\`
**Issue**: ${issue.description}
**Impact**: ${issue.impact}
${formatIssueDetails(issue)}
**Recommendation**:
${formatRecommendation(issue.recommendation)}

---
`.trim();
}

function formatIssueTitle(issue) {
  const titles = {
    "deep-column-chain": "Deep Computed Column Chain",
    "rollup-without-relation": "Rollup Without Relation",
    "circular-dependency": "Circular Dependency Detected",
    "row-limit-critical": "Row Limit Critical",
    "heavy-ai-usage": "Heavy AI Column Usage",
    "component-overload": "Component Overload",
    "inline-collection-overload": "Inline Collection Overload"
  };
  return titles[issue.type] || issue.type;
}

function formatIssueDetails(issue) {
  if (!issue.details) return "";

  const lines = [];

  // Format different detail types
  if (issue.details.chain) {
    lines.push(`**Column Chain**: ${issue.details.chain.join(" → ")}`);
  }

  if (issue.details.relations) {
    lines.push(`**Relations**: ${issue.details.relations.join(", ")}`);
  }

  if (issue.details.aiColumns) {
    const aiList = issue.details.aiColumns
      .map(ai => `${ai.name} (${ai.type})`)
      .join(", ");
    lines.push(`**AI Columns**: ${aiList}`);
  }

  if (issue.details.currentRows && issue.details.limit) {
    lines.push(`**Current Rows**: ${issue.details.currentRows.toLocaleString()}`);
    lines.push(`**Limit**: ${issue.details.limit.toLocaleString()}`);
    lines.push(`**Utilization**: ${issue.details.utilization}%`);
  }

  return lines.length > 0 ? lines.join("\n") + "\n" : "";
}

function formatRecommendation(recommendation) {
  // If recommendation has multiple lines or steps, format as list
  if (recommendation.includes("\n")) {
    return recommendation
      .split("\n")
      .map(line => {
        line = line.trim();
        if (line.match(/^\d+\./)) return line; // Already numbered
        if (line.length > 0) return `- ${line}`;
        return "";
      })
      .join("\n");
  }
  return recommendation;
}
```

---

### 4. Warnings Section

List all warning severity issues.

```markdown
## Warnings (🟡)

### 1. {Warning Title}
**Location**: {location}
**Issue**: {description}
**Impact**: {impact}
**Recommendation**: {fix}

---

### 2. {Next Warning}
...
```

**Same formatting as critical**, just different severity level.

---

### 5. Optimizations Section

List optimization opportunities.

```markdown
## Optimizations (🟢)

### 1. {Optimization Title}
**Location**: {location}
**Issue**: {description}
**Impact**: {impact}
**Recommendation**: {fix}

---
```

---

### 6. Data Structure Summary

Statistical overview of the app's data architecture.

```markdown
## Data Structure Summary

| Metric | Value |
|--------|-------|
| **Total Tables** | {count} |
| **Total Columns** | {count} |
| **Computed Columns** | {count} ({percentage}%) |
| **Relation Columns** | {count} |
| **AI Columns** | {count} |
| **Deepest Column Chain** | {depth} layers |
| **Avg Columns per Table** | {avg} |
| **Avg Relations per Table** | {avg} |

### Tables

| Table | Rows | Columns | Computed | Relations | Issues |
|-------|------|---------|----------|-----------|--------|
| {table1} | {rows} | {cols} | {computed} | {relations} | {issues} |
| {table2} | ... | ... | ... | ... | ... |
```

**Generation**:
```javascript
function generateDataSummary(dataAnalysis) {
  const { summary, tableAnalysis } = dataAnalysis;

  // Main summary table
  const mainTable = `
| Metric | Value |
|--------|-------|
| **Total Tables** | ${summary.totalTables} |
| **Total Columns** | ${summary.totalColumns} |
| **Computed Columns** | ${summary.computedColumns} (${summary.computedPercentage}%) |
| **Relation Columns** | ${summary.relationColumns} |
| **AI Columns** | ${summary.aiColumns} |
| **Deepest Column Chain** | ${summary.deepestChain} layers |
| **Avg Columns per Table** | ${summary.avgColumnsPerTable} |
| **Avg Relations per Table** | ${summary.avgRelationsPerTable} |
`.trim();

  // Tables detail
  let tablesTable = `
| Table | Rows | Columns | Computed | Relations | Issues |
|-------|------|---------|----------|-----------|--------|
`;

  tableAnalysis.forEach(table => {
    tablesTable += `| ${table.tableName} | ${table.rowCount} | ${table.columnCount} | ${table.computedColumns} | ${table.relationCount} | ${table.issues} |\n`;
  });

  return `
## Data Structure Summary

${mainTable}

### Tables

${tablesTable.trim()}
`.trim();
}
```

---

### 7. Layout Summary

Overview of screen structure.

```markdown
## Layout Summary

| Screen | Type | Components | Inline Collections | Issues |
|--------|------|------------|-------------------|--------|
| {screen1} | {type} | {count} | {count} | {issues} |
| {screen2} | ... | ... | ... | ... |
```

---

### 8. Performance Score

Visual score breakdown.

```markdown
## Performance Score: {overall}/100

**Overall Health**: {health status}

### Breakdown

| Category | Score | Status |
|----------|-------|--------|
| **Data Architecture** | {score}/40 | {status} |
| **Layout Design** | {score}/40 | {status} |
| **Best Practices** | {score}/20 | {status} |

### Score Guide
- **90-100**: Excellent - Well-architected, minimal issues
- **75-89**: Good - Some optimizations available
- **60-74**: Needs Attention - Performance issues present
- **Below 60**: Critical - Significant problems requiring immediate action
```

**Score Calculation**:
```javascript
function calculateOverallScore(dataScore, layoutScore, practicesScore) {
  return dataScore + layoutScore + practicesScore;
}

function getScoreStatus(score, maxScore) {
  const percentage = (score / maxScore) * 100;
  if (percentage >= 90) return "✅ Excellent";
  if (percentage >= 75) return "🟢 Good";
  if (percentage >= 60) return "🟡 Needs Work";
  return "🔴 Critical";
}
```

---

### 9. Priority Actions

Ordered list of top actions to take.

```markdown
## Priority Actions

1. **{Action 1}** - {Brief description}
2. **{Action 2}** - {Brief description}
3. **{Action 3}** - {Brief description}
4. **{Action 4}** - {Brief description}
5. **{Action 5}** - {Brief description}

---
```

**Generation Logic**:
```javascript
function generatePriorityActions(allIssues) {
  const actions = [];

  // Critical issues first
  const criticals = allIssues.filter(i => i.severity === "critical");
  criticals.slice(0, 3).forEach(issue => {
    actions.push({
      text: `Fix ${formatIssueTitle(issue).toLowerCase()}`,
      detail: `in ${issue.location}`
    });
  });

  // High-impact warnings
  const warnings = allIssues.filter(i => i.severity === "warning");

  // Prioritize by impact
  const highImpact = warnings.filter(i =>
    i.type.includes("ai") ||
    i.type.includes("rollup") ||
    i.type.includes("chain")
  );

  highImpact.slice(0, 5 - actions.length).forEach(issue => {
    actions.push({
      text: `Address ${formatIssueTitle(issue).toLowerCase()}`,
      detail: `in ${issue.location}`
    });
  });

  // Fill remaining with top warnings
  if (actions.length < 5) {
    warnings.slice(0, 5 - actions.length).forEach(issue => {
      actions.push({
        text: `Optimize ${formatIssueTitle(issue).toLowerCase()}`,
        detail: `in ${issue.location}`
      });
    });
  }

  return actions.slice(0, 5); // Max 5 actions
}
```

---

### 10. Footer

```markdown
---

## Additional Resources

- **[Analysis Patterns](analysis-patterns.md)** - Deep dive on each issue type
- **[Recommendations](recommendations.md)** - Detailed fix strategies
- **[Glide Documentation](https://www.glideapps.com/docs)** - Official Glide docs
- **[Glide Community](https://community.glideapps.com)** - Get help from experts

---

*Generated by Glide App Audit Tool on {date}*
*Need help? Run `/app-audit` again or ask for assistance implementing fixes.*
```

---

## Complete Report Template

```javascript
function generateReport(analysisResults) {
  const {
    appId,
    appName,
    auditDate,
    dataAnalysis,
    layoutAnalysis
  } = analysisResults;

  // Combine all issues
  const allIssues = [
    ...dataAnalysis.issues,
    ...layoutAnalysis.issues
  ];

  // Sort by severity
  const criticals = allIssues.filter(i => i.severity === "critical");
  const warnings = allIssues.filter(i => i.severity === "warning");
  const optimizations = allIssues.filter(i => i.severity === "optimization");

  // Calculate overall score
  const overallScore = calculateOverallScore(
    dataAnalysis.scores.dataArchitecture,
    layoutAnalysis.scores.layoutDesign,
    dataAnalysis.scores.bestPractices
  );

  const healthStatus = determineHealthStatus(overallScore);

  // Build report sections
  const report = `
# Glide App Audit Report

**App**: ${appName || "Unnamed App"}
**App ID**: \`${appId}\`
**Audit Date**: ${formatDate(auditDate)}
**Overall Health**: ${healthStatus}

---

${generateExecutiveSummarySection(allIssues, overallScore)}

---

${criticals.length > 0 ? generateCriticalSection(criticals) + "\n---\n" : ""}

${warnings.length > 0 ? generateWarningsSection(warnings) + "\n---\n" : ""}

${optimizations.length > 0 ? generateOptimizationsSection(optimizations) + "\n---\n" : ""}

${generateDataSummary(dataAnalysis)}

---

${generateLayoutSummary(layoutAnalysis)}

---

${generatePerformanceScore(dataAnalysis.scores, layoutAnalysis.scores, overallScore)}

---

${generatePriorityActionsSection(allIssues)}

---

## Additional Resources

- **[Analysis Patterns](analysis-patterns.md)** - Deep dive on each issue type
- **[Recommendations](recommendations.md)** - Detailed fix strategies
- **[Glide Documentation](https://www.glideapps.com/docs)** - Official Glide docs
- **[Glide Community](https://community.glideapps.com)** - Get help from experts

---

*Generated by Glide App Audit Tool on ${formatDate(auditDate)}*
*Need help implementing fixes? Ask for assistance or refer to the recommendations guide.*
`.trim();

  return report;
}
```

---

## Formatting Utilities

```javascript
function formatDate(isoDate) {
  const date = new Date(isoDate);
  return date.toLocaleDateString("en-US", {
    year: "numeric",
    month: "long",
    day: "numeric"
  });
}

function pluralize(count, singular, plural) {
  return count === 1 ? singular : (plural || singular + "s");
}

function groupIssuesByType(issues) {
  const groups = {};
  issues.forEach(issue => {
    if (!groups[issue.type]) {
      groups[issue.type] = [];
    }
    groups[issue.type].push(issue);
  });
  return groups;
}

function formatIssueType(type) {
  // Convert kebab-case to readable text
  return type
    .split("-")
    .map(word => word.charAt(0).toUpperCase() + word.slice(1))
    .join(" ");
}
```

---

## Special Case Handling

### No Issues Found

If no issues detected:

```markdown
# Glide App Audit Report

**App**: {appName}
**Overall Health**: 🟢 Excellent

## Executive Summary

Congratulations! Your app has no significant performance issues. The data architecture and layout design follow Glide best practices.

## Performance Score: {score}/100

Your app is well-optimized and performing excellently.

## Recommendations

Continue monitoring as your app grows:
- Run periodic audits (monthly for active apps)
- Watch row counts as data increases
- Test performance with realistic data volumes
- Maintain current best practices
```

### Partial Analysis

If some analysis failed:

```markdown
**Note**: This audit includes partial results. {description of what was analyzed and what wasn't}.

For complete analysis, please ensure:
- App has data in tables (for detail screen analysis)
- API access is available (for table schema analysis)
- Browser can fully load all screens
```

---

## Output Delivery

Return the markdown report as a string to be displayed to the user.

```javascript
// Return report
return {
  report: generatedMarkdown,
  summary: {
    overallScore,
    healthStatus,
    criticalCount: criticals.length,
    warningCount: warnings.length,
    optimizationCount: optimizations.length
  }
};
```

---

## Example Complete Report

See [SKILL.md](../SKILL.md#example-output) for a full example report.

---

## References

- [SKILL.md](../SKILL.md) - Main audit documentation
- [Analysis Patterns](../analysis-patterns.md) - Issue type details
- [Recommendations](../recommendations.md) - Fix strategies
