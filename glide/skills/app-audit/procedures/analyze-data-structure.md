# Analyze Data Structure Procedure

Analyze app data structure to detect performance anti-patterns and calculate severity scores.

## Overview

This procedure takes the extracted app data and performs deep analysis to identify:
- Deep computed column chains
- Relation proliferation
- Rollup misconfigurations
- AI column overuse
- Query column overuse
- Row limit issues

## Input

Structured app data from `fetch-app-data.md`:
```javascript
{
  appId: "abc123",
  tables: [
    {
      tableID: "table123",
      name: "Tasks",
      rowCount: 150,
      columns: [...],
      dependencyGraph: {...}
    }
  ]
}
```

## Output

Analysis results object:
```javascript
{
  issues: [
    {
      severity: "critical" | "warning" | "optimization",
      category: "data",
      type: "deep-column-chain",
      location: "Tasks table",
      description: "6-layer computed column dependency chain",
      details: {
        columns: ["Due Date", "Days Until Due", ...],
        depth: 6
      },
      impact: "High latency on all data operations",
      recommendation: "Flatten to 2-3 layers maximum..."
    }
  ],
  scores: {
    dataArchitecture: 55,
    overall: 65
  },
  summary: {
    totalTables: 8,
    totalColumns: 156,
    computedColumns: 72,
    relationColumns: 18,
    aiColumns: 3,
    deepestChain: 6
  }
}
```

## Analysis Algorithms

### 1. Compute Column Depth Analysis

**Goal**: Find the longest dependency chains in each table.

**Algorithm**:
```javascript
function analyzeColumnDepth(table) {
  const issues = [];
  const graph = table.dependencyGraph;

  // 1. Identify all leaf nodes (basic columns with no dependencies)
  const leafNodes = Object.keys(graph).filter(
    col => graph[col].dependencies.length === 0
  );

  // 2. For each leaf, compute max depth to all reachable nodes
  const depths = {};

  function computeDepth(column, visited = new Set()) {
    // Prevent circular dependencies
    if (visited.has(column)) {
      issues.push({
        severity: "critical",
        type: "circular-dependency",
        location: `${table.name} table`,
        description: `Circular dependency detected involving ${column}`,
        details: { columns: Array.from(visited) }
      });
      return Infinity;
    }

    // If already computed, return cached value
    if (depths[column] !== undefined) {
      return depths[column];
    }

    // Leaf node has depth 0
    if (graph[column].dependencies.length === 0) {
      depths[column] = 0;
      return 0;
    }

    // Compute depth as 1 + max depth of dependencies
    visited.add(column);
    const depDepths = graph[column].dependencies.map(dep =>
      computeDepth(dep, new Set(visited))
    );
    visited.delete(column);

    const maxDepth = Math.max(...depDepths) + 1;
    depths[column] = maxDepth;
    return maxDepth;
  }

  // 3. Compute depths for all columns
  Object.keys(graph).forEach(col => computeDepth(col));

  // 4. Find the deepest chains
  const deepestDepth = Math.max(...Object.values(depths), 0);
  const deepColumns = Object.entries(depths)
    .filter(([col, depth]) => depth >= 4)
    .map(([col, depth]) => ({ column: col, depth }));

  // 5. Report issues
  deepColumns.forEach(({ column, depth }) => {
    // Reconstruct chain
    const chain = reconstructChain(column, graph);

    const severity = depth >= 6 ? "critical" : "warning";

    issues.push({
      severity,
      category: "data",
      type: "deep-column-chain",
      location: `${table.name} table`,
      description: `${depth}-layer computed column dependency chain`,
      details: {
        leafColumn: column,
        chain,
        depth
      },
      impact: depth >= 6
        ? "High latency on all data operations, slow screen loading"
        : "Moderate latency, performance degrades with data growth",
      recommendation:
        "Flatten to 2-3 layers maximum. Combine logic with If-Then-Else columns."
    });
  });

  return { issues, deepestDepth };
}

function reconstructChain(column, graph) {
  // Walk backwards from column to leaf node
  const chain = [column];
  let current = column;

  while (graph[current].dependencies.length > 0) {
    // Take first dependency (simplification)
    current = graph[current].dependencies[0];
    chain.push(current);
  }

  return chain.reverse(); // Return leaf → column order
}
```

---

### 2. Relation Count Analysis

**Goal**: Count relations per table, flag excessive relations.

**Algorithm**:
```javascript
function analyzeRelations(table) {
  const issues = [];

  // Count relation-type columns
  const relationColumns = table.columns.filter(
    col => col.type === "relation"
  );

  const relationCount = relationColumns.length;

  if (relationCount >= 8) {
    issues.push({
      severity: "critical",
      category: "data",
      type: "relation-proliferation",
      location: `${table.name} table`,
      description: `${relationCount} relation columns detected`,
      details: {
        relations: relationColumns.map(col => col.name),
        count: relationCount
      },
      impact: "High query load, slow screen rendering, memory overhead",
      recommendation:
        "Consolidate relations. Keep only 3-4 essential relations. " +
        "Access other data via sub-relations or separate screens."
    });
  } else if (relationCount >= 5) {
    issues.push({
      severity: "warning",
      category: "data",
      type: "high-relation-count",
      location: `${table.name} table`,
      description: `${relationCount} relation columns detected`,
      details: {
        relations: relationColumns.map(col => col.name),
        count: relationCount
      },
      impact: "Increased query load on screens displaying this table",
      recommendation:
        "Review if all relations are necessary. Consider consolidating."
    });
  }

  return { issues, relationCount };
}
```

---

### 3. Rollup Configuration Analysis

**Goal**: Detect rollups operating on tables instead of relations.

**Algorithm**:
```javascript
function analyzeRollups(table) {
  const issues = [];

  // Find rollup columns
  const rollupColumns = table.columns.filter(
    col => col.computedType === "rollup"
  );

  rollupColumns.forEach(rollup => {
    // Check if source is a relation or table
    const sourceType = rollup.config?.sourceType; // "relation" or "table"

    if (sourceType === "table") {
      issues.push({
        severity: "critical",
        category: "data",
        type: "rollup-without-relation",
        location: `${table.name} table, ${rollup.name} column`,
        description: "Rollup operating on entire table instead of relation",
        details: {
          rollupColumn: rollup.name,
          sourceTable: rollup.config.sourceTable,
          function: rollup.config.function // count, sum, etc.
        },
        impact:
          "Processes all rows unnecessarily. Returns same value for all rows. " +
          "Logical error - data is not filtered to this record.",
        recommendation:
          "1. Create a relation column matching this table to target table\n" +
          "2. Update rollup to operate on the relation\n" +
          "3. Result: Each row shows only its related data"
      });
    }
  });

  return { issues, rollupCount: rollupColumns.length };
}
```

---

### 4. AI Column Usage Analysis

**Goal**: Count AI columns per table, flag overuse.

**Algorithm**:
```javascript
function analyzeAIColumns(table) {
  const issues = [];

  // AI column types
  const aiTypes = [
    "generate-text",
    "image-to-text",
    "document-to-text",
    "audio-to-text",
    "text-to-boolean",
    "text-to-choice",
    "text-to-json",
    "text-to-number",
    "text-to-date",
    "text-to-texts"
  ];

  const aiColumns = table.columns.filter(
    col => aiTypes.includes(col.computedType)
  );

  const aiCount = aiColumns.length;

  if (aiCount >= 5) {
    issues.push({
      severity: "critical",
      category: "data",
      type: "heavy-ai-usage",
      location: `${table.name} table`,
      description: `${aiCount} AI columns detected`,
      details: {
        aiColumns: aiColumns.map(col => ({
          name: col.name,
          type: col.computedType
        })),
        count: aiCount,
        estimatedLatency: `${aiCount * 2}-${aiCount * 3} seconds per row`
      },
      impact:
        "Very slow data operations (6-18 seconds per row). " +
        "High AI credit consumption. Blocks other operations.",
      recommendation:
        "Consolidate AI operations. Use comprehensive prompts that combine " +
        "multiple outputs into one AI call. Limit to 1-2 AI columns per table."
    });
  } else if (aiCount >= 3) {
    issues.push({
      severity: "warning",
      category: "data",
      type: "moderate-ai-usage",
      location: `${table.name} table`,
      description: `${aiCount} AI columns detected`,
      details: {
        aiColumns: aiColumns.map(col => ({
          name: col.name,
          type: col.computedType
        })),
        count: aiCount,
        estimatedLatency: `${aiCount * 2}-${aiCount * 3} seconds per row`
      },
      impact: "Slow data operations, increased AI costs",
      recommendation:
        "Consider consolidating AI calls with comprehensive prompts. " +
        "Use structured output parsing to extract multiple values from one AI call."
    });
  }

  return { issues, aiCount };
}
```

---

### 5. Query Column Analysis

**Goal**: Detect multiple Query columns (should use relations instead).

**Algorithm**:
```javascript
function analyzeQueryColumns(table) {
  const issues = [];

  const queryColumns = table.columns.filter(
    col => col.computedType === "query"
  );

  const queryCount = queryColumns.length;

  if (queryCount >= 2) {
    issues.push({
      severity: "warning",
      category: "data",
      type: "query-overuse",
      location: `${table.name} table`,
      description: `${queryCount} Query columns detected`,
      details: {
        queryColumns: queryColumns.map(col => col.name),
        count: queryCount
      },
      impact:
        "Each Query column triggers separate database query. " +
        `${queryCount} queries per row = ${queryCount * 200}ms+ latency.`,
      recommendation:
        "Replace Query columns with one Relation + multiple Lookups:\n" +
        "1. Create Relation column to target table\n" +
        "2. Create Lookup columns via that relation\n" +
        "Result: 1 query instead of " + queryCount
    });
  }

  return { issues, queryCount };
}
```

---

### 6. Row Limit Analysis

**Goal**: Check if tables are approaching row limits.

**Algorithm**:
```javascript
function analyzeRowLimits(table, planLimits) {
  const issues = [];

  // Plan limits (example values, should be configured)
  const limits = planLimits || {
    free: 25000,
    pro: 100000,
    business: 1000000
  };

  // Assume the current plan limit (could be passed as parameter)
  const currentLimit = limits.pro; // Example: Pro plan

  const rowCount = table.rowCount;
  const utilization = (rowCount / currentLimit) * 100;

  if (utilization >= 95) {
    issues.push({
      severity: "critical",
      category: "data",
      type: "row-limit-critical",
      location: `${table.name} table`,
      description: "Table at ${utilization.toFixed(1)}% of row limit",
      details: {
        currentRows: rowCount,
        limit: currentLimit,
        utilization: utilization.toFixed(1),
        remainingRows: currentLimit - rowCount
      },
      impact:
        "Risk of data loss. New rows will fail to sync. " +
        "Severe performance degradation near limit.",
      recommendation:
        "URGENT: Implement data archival or cleanup immediately. " +
        "Options: Archive old data, delete obsolete records, " +
        "upgrade to higher tier, use Big Tables."
    });
  } else if (utilization >= 80) {
    issues.push({
      severity: "warning",
      category: "data",
      type: "row-limit-warning",
      location: `${table.name} table`,
      description: `Table at ${utilization.toFixed(1)}% of row limit`,
      details: {
        currentRows: rowCount,
        limit: currentLimit,
        utilization: utilization.toFixed(1),
        remainingRows: currentLimit - rowCount
      },
      impact:
        "Approaching row limit. Performance will degrade further. " +
        "Plan data management strategy now.",
      recommendation:
        "Implement data retention policy. Options:\n" +
        "- Archive old records to separate table\n" +
        "- Create scheduled cleanup workflow\n" +
        "- Consider upgrading plan for higher limits"
    });
  }

  return { issues, rowCount, utilization };
}
```

---

## Main Analysis Function

Orchestrate all analysis algorithms:

```javascript
function analyzeDataStructure(appData, options = {}) {
  const allIssues = [];
  const tableAnalysis = [];

  // Analyze each table
  appData.tables.forEach(table => {
    const tableIssues = [];

    // 1. Column depth analysis
    const depthResult = analyzeColumnDepth(table);
    tableIssues.push(...depthResult.issues);

    // 2. Relation count
    const relationResult = analyzeRelations(table);
    tableIssues.push(...relationResult.issues);

    // 3. Rollup configuration
    const rollupResult = analyzeRollups(table);
    tableIssues.push(...rollupResult.issues);

    // 4. AI column usage
    const aiResult = analyzeAIColumns(table);
    tableIssues.push(...aiResult.issues);

    // 5. Query columns
    const queryResult = analyzeQueryColumns(table);
    tableIssues.push(...queryResult.issues);

    // 6. Row limits
    const rowLimitResult = analyzeRowLimits(table, options.planLimits);
    tableIssues.push(...rowLimitResult.issues);

    // Store table-level metrics
    tableAnalysis.push({
      tableName: table.name,
      rowCount: table.rowCount,
      columnCount: table.columns.length,
      computedColumns: table.columns.filter(c => c.type === "computed").length,
      relationCount: relationResult.relationCount,
      aiColumnCount: aiResult.aiCount,
      deepestChain: depthResult.deepestDepth,
      issues: tableIssues.length
    });

    allIssues.push(...tableIssues);
  });

  // Calculate summary stats
  const summary = calculateSummary(appData, tableAnalysis);

  // Calculate scores
  const scores = calculateScores(allIssues, summary);

  return {
    issues: allIssues,
    tableAnalysis,
    summary,
    scores
  };
}
```

---

## Scoring System

Calculate performance scores based on issues found.

```javascript
function calculateScores(issues, summary) {
  // Data Architecture score (40 points max)
  let dataScore = 40;

  // Deduct points based on severity and count
  const criticalDataIssues = issues.filter(
    i => i.severity === "critical" && i.category === "data"
  );
  const warningDataIssues = issues.filter(
    i => i.severity === "warning" && i.category === "data"
  );

  dataScore -= criticalDataIssues.length * 10; // -10 per critical
  dataScore -= warningDataIssues.length * 3;   // -3 per warning
  dataScore = Math.max(0, dataScore); // Floor at 0

  // Best Practices score (20 points max)
  let practicesScore = 20;

  // Deduct for anti-patterns
  if (summary.deepestChain >= 4) practicesScore -= 5;
  if (summary.aiColumns > 5) practicesScore -= 5;
  if (summary.avgRelationsPerTable > 4) practicesScore -= 5;

  practicesScore = Math.max(0, practicesScore);

  // Note: Layout score calculated in analyze-layout procedure
  // Overall score will be: dataScore + layoutScore + practicesScore

  return {
    dataArchitecture: dataScore,
    bestPractices: practicesScore,
    // layoutDesign: calculated later
    // overall: calculated after layout analysis
  };
}

function calculateSummary(appData, tableAnalysis) {
  const totalColumns = tableAnalysis.reduce((sum, t) => sum + t.columnCount, 0);
  const totalComputed = tableAnalysis.reduce((sum, t) => sum + t.computedColumns, 0);
  const totalRelations = tableAnalysis.reduce((sum, t) => sum + t.relationCount, 0);
  const totalAI = tableAnalysis.reduce((sum, t) => sum + t.aiColumnCount, 0);
  const deepestChain = Math.max(...tableAnalysis.map(t => t.deepestChain), 0);

  return {
    totalTables: appData.tables.length,
    totalColumns,
    computedColumns: totalComputed,
    computedPercentage: ((totalComputed / totalColumns) * 100).toFixed(1),
    relationColumns: totalRelations,
    aiColumns: totalAI,
    deepestChain,
    avgColumnsPerTable: (totalColumns / appData.tables.length).toFixed(1),
    avgRelationsPerTable: (totalRelations / appData.tables.length).toFixed(1)
  };
}
```

---

## Output Format

Return structured analysis:

```javascript
{
  issues: [
    {
      severity: "critical",
      category: "data",
      type: "deep-column-chain",
      location: "Tasks table",
      description: "6-layer computed column dependency chain",
      details: { ... },
      impact: "...",
      recommendation: "..."
    },
    // ... more issues
  ],
  tableAnalysis: [
    {
      tableName: "Tasks",
      rowCount: 150,
      columnCount: 28,
      computedColumns: 15,
      relationCount: 2,
      aiColumnCount: 1,
      deepestChain: 6,
      issues: 3
    },
    // ... more tables
  ],
  summary: {
    totalTables: 8,
    totalColumns: 156,
    computedColumns: 72,
    computedPercentage: "46.2",
    relationColumns: 18,
    aiColumns: 3,
    deepestChain: 6,
    avgColumnsPerTable: "19.5",
    avgRelationsPerTable: "2.3"
  },
  scores: {
    dataArchitecture: 25,
    bestPractices: 15,
    // layoutDesign: added later
    // overall: calculated at end
  }
}
```

---

## Performance Considerations

### Large Apps

For apps with 50+ tables and 500+ columns:
- Analysis can take 1-2 minutes
- Dependency graph construction is O(n²) worst case
- Consider progress updates

### Memory Usage

Dependency graphs for large tables:
- Store only necessary information
- Limit depth calculation recursion
- Clear cached data after each table

---

## Error Handling

### Incomplete Data

If column type information is missing:
- Skip specific analyses that require it
- Note in report: "Limited analysis - column types not available"
- Proceed with available data

### Invalid Dependency Graphs

If circular dependencies or malformed data:
- Flag as critical issue
- Continue with other analyses
- Don't crash entire audit

---

## References

- [Analysis Patterns](../analysis-patterns.md) - Detailed anti-pattern descriptions
- [Computed Columns Skill](../../computed-columns/SKILL.md)
- [Data Modeling Skill](../../data-modeling/SKILL.md)
