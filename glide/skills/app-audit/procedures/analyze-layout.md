# Analyze Layout Procedure

Analyze app layout and screens using browser automation to detect UI performance anti-patterns.

## ⚠️ CRITICAL: Read-Only Operations

**This procedure must ONLY inspect the layout. NEVER modify anything:**
- ✅ Navigate between screens (READ)
- ✅ Click to view component settings (READ)
- ✅ Take snapshots of UI (READ)
- ✅ Count components and fields (READ)
- ❌ NEVER add, delete, or edit components
- ❌ NEVER change component settings
- ❌ NEVER click Save or Update buttons
- ❌ NEVER modify screen structure

**If you accidentally open an edit dialog, press ESC to close without saving.**

## Overview

This procedure inspects the app's layout editor to identify:
- Collection field density (too many fields per card/list)
- Inline collection overload (too many nested collections)
- Component overload (too many components per screen)
- Unoptimized table views (showing expensive computed columns)

## Prerequisites

- Browser is authenticated to Glide
- App data has been fetched (need App ID)
- Browser is currently open (from fetch-app-data procedure)

## Input

```javascript
{
  appId: "abc123def456",
  appName: "Task Manager",
  tables: [/* table data for reference */]
}
```

## Output

```javascript
{
  issues: [
    {
      severity: "warning",
      category: "layout",
      type: "dense-collection",
      location: "Tasks screen (Card collection)",
      description: "Showing 12 fields per card",
      details: {
        screenName: "Tasks",
        collectionType: "card",
        fieldCount: 12
      },
      impact: "Heavy rendering, slow scrolling, information overload",
      recommendation: "Reduce to 3-5 key fields..."
    }
  ],
  screenAnalysis: [
    {
      screenName: "Tasks",
      screenType: "collection",
      componentCount: 15,
      inlineCollections: 0,
      collectionFieldCount: 12,
      issues: 1
    }
  ],
  scores: {
    layoutDesign: 30
  }
}
```

## Procedure Steps

### Step 1: Navigate to Layout Editor

Navigate from Data Editor to Layout Editor.

**URL**:
```
https://go.glideapps.com/app/{appId}/layout
```

**Browser commands**:
```
NAVIGATE to https://go.glideapps.com/app/{appId}/layout
WAIT for Layout Editor to load
VERIFY: Can see screen list, preview pane, component list
```

**Expected state**:
- Layout Editor loaded
- Can see tabs/screens in navigation section
- Preview pane shows app interface
- Component list visible in left sidebar

---

### Step 2: Identify All Screens

Get list of all screens/tabs in the app.

**Browser commands**:
```
SNAPSHOT of navigation section (tabs/menu)
EXTRACT list of screen names
```

**Expected output**:
```javascript
const screens = [
  { name: "Tasks", type: "tab", visible: true },
  { name: "Projects", type: "tab", visible: true },
  { name: "Settings", type: "menu", visible: true }
];
```

**Note**: Screens can be:
- **Tabs** (bottom navigation)
- **Menu items** (hamburger menu)
- **Detail screens** (accessed by clicking items)
- **Form screens** (add/edit screens)

---

### Step 3: Analyze Each Screen

For each screen, navigate to it and inspect its structure.

#### Step 3a: Navigate to Screen

**Browser commands**:
```
CLICK screen/tab in navigation
WAIT for screen to load in preview
WAIT for component list to update
```

**Verification**:
- Preview shows the selected screen
- Component list (left sidebar) shows components for this screen

#### Step 3b: Identify Screen Type

Determine what type of screen this is:

**Collection Screen**:
- Shows list/grid/table of items
- Has collection component (Card, List, Table, etc.)

**Detail Screen**:
- Shows single item details
- Click on item in collection to navigate here

**Custom Screen**:
- Blank canvas with custom layout
- Dashboard, forms, custom layouts

**Browser method**:
```
SNAPSHOT component list
ANALYZE types of components:
  - If has Collection component → Collection screen
  - If has Title + Fields → Detail screen
  - If mixed layout → Custom screen
```

#### Step 3c: Count Components

Count total components on the screen.

**Browser commands**:
```
SNAPSHOT component list (left sidebar)
COUNT number of components
```

**Component count includes**:
- Title components
- Collection components
- Content components (Big Numbers, Progress, etc.)
- Layout components (Containers, Separators)
- Action components (Buttons, Links)
- Form components

**Flag if**:
- 20+ components → Warning
- 30+ components → Critical

#### Step 3d: Analyze Collections

If screen has a Collection component:

**For Card/List collections**:
```
CLICK collection component
OPEN settings panel (right sidebar)
SNAPSHOT settings
FIND "Fields" or "Display" section
COUNT number of visible fields
```

**Extract**:
- Collection style (Card, List, Table, etc.)
- Number of fields displayed
- Which columns are shown

**Flag if**:
- Card/List showing 10+ fields → Optimization
- Card/List showing 15+ fields → Warning

**For Table collections**:
```
SNAPSHOT visible columns
IDENTIFY column types (basic, computed, AI, rollup)
COUNT expensive columns (AI, Rollup)
```

**Flag if**:
- Table showing 3+ computed columns → Optimization
- Table showing AI columns → Warning
- Table showing Rollup columns → Warning

#### Step 3e: Count Inline Collections

Look for inline collection components.

**Browser commands**:
```
In component list, COUNT components of type:
  - Collection
  - Inline Collection (if named differently)
```

**Note**: Regular collection at top of screen doesn't count. Only nested/inline collections.

**Flag if**:
- 3+ inline collections on one screen → Warning
- 5+ inline collections → Critical

#### Step 3f: Identify Heavy Components

Look for computationally expensive components.

**Heavy components**:
- Charts (Bar Chart, Line Chart, Pie Chart)
- Maps
- Web Embeds
- Video players

**Count these separately**, as they add rendering cost.

---

### Step 4: Analyze Detail Screens

Detail screens require navigation from collection screens.

**Process**:
```
1. Navigate to collection screen (e.g., Tasks)
2. In preview pane, CLICK on an item
3. Preview navigates to detail screen
4. Component list updates to show detail screen components
5. Analyze detail screen (repeat Step 3)
6. Navigate back to collection
```

**Challenges**:
- Need actual data in preview to click
- If no data, detail screen can't be accessed
- May need to add test data first

**Workaround if no data**:
- Skip detail screen analysis
- Note in report: "Detail screens not analyzed (no data available)"

---

### Step 5: Analyze Form Screens

Form screens (Add/Edit) are accessed via actions.

**Process**:
```
1. On detail screen, CLICK "Edit" button in preview
2. Or on collection screen, CLICK "Add" button
3. Preview navigates to form screen
4. Analyze components (repeat Step 3)
```

**Note**: Form screens typically have fewer components, less likely to have issues.

---

### Step 6: Score Layout Performance

Calculate layout score based on issues found.

**Algorithm**:
```javascript
function calculateLayoutScore(issues, screenAnalysis) {
  // Start with 40 points (max for layout)
  let layoutScore = 40;

  // Critical layout issues
  const criticalLayout = issues.filter(
    i => i.severity === "critical" && i.category === "layout"
  );

  // Warning layout issues
  const warningLayout = issues.filter(
    i => i.severity === "warning" && i.category === "layout"
  );

  // Optimization issues
  const optimizationLayout = issues.filter(
    i => i.severity === "optimization" && i.category === "layout"
  );

  // Deduct points
  layoutScore -= criticalLayout.length * 10;
  layoutScore -= warningLayout.length * 3;
  layoutScore -= optimizationLayout.length * 1;

  // Floor at 0
  layoutScore = Math.max(0, layoutScore);

  return layoutScore;
}
```

---

## Analysis Algorithms

### 1. Collection Field Density Check

```javascript
function analyzeCollectionDensity(screen, collection) {
  const issues = [];

  if (collection.type === "card" || collection.type === "list") {
    const fieldCount = collection.visibleFields.length;

    if (fieldCount >= 15) {
      issues.push({
        severity: "warning",
        category: "layout",
        type: "dense-collection",
        location: `${screen.name} screen (${collection.type} collection)`,
        description: `Showing ${fieldCount} fields per ${collection.type}`,
        details: {
          screenName: screen.name,
          collectionType: collection.type,
          fieldCount,
          fields: collection.visibleFields
        },
        impact:
          "Heavy rendering load, slow scrolling, information overload, " +
          "poor user experience on mobile",
        recommendation:
          "Reduce to 3-5 key fields. Show only essential information: " +
          "title, status, key date, assignee. Move detailed info to detail screen."
      });
    } else if (fieldCount >= 10) {
      issues.push({
        severity: "optimization",
        category: "layout",
        type: "moderately-dense-collection",
        location: `${screen.name} screen (${collection.type} collection)`,
        description: `Showing ${fieldCount} fields per ${collection.type}`,
        details: {
          screenName: screen.name,
          collectionType: collection.type,
          fieldCount,
          fields: collection.visibleFields
        },
        impact: "Slower rendering, less scannable",
        recommendation:
          "Consider reducing to 5-7 fields for better scannability."
      });
    }
  }

  return issues;
}
```

---

### 2. Inline Collection Overload Check

```javascript
function analyzeInlineCollections(screen) {
  const issues = [];

  // Count collection components (excluding the main one)
  const inlineCollections = screen.components.filter(
    c => c.type === "collection" && c.isInline
  );

  const count = inlineCollections.length;

  if (count >= 5) {
    issues.push({
      severity: "critical",
      category: "layout",
      type: "inline-collection-overload",
      location: `${screen.name} screen`,
      description: `${count} inline collections on one screen`,
      details: {
        screenName: screen.name,
        inlineCollections: inlineCollections.map(c => c.title || c.source),
        count
      },
      impact:
        `${count} separate queries on page load. Very slow screen rendering. ` +
        "Poor user experience, high query load.",
      recommendation:
        "Limit to 1-2 inline collections. Use navigation buttons to " +
        "separate screens for other collections."
    });
  } else if (count >= 3) {
    issues.push({
      severity: "warning",
      category: "layout",
      type: "multiple-inline-collections",
      location: `${screen.name} screen`,
      description: `${count} inline collections on one screen`,
      details: {
        screenName: screen.name,
        inlineCollections: inlineCollections.map(c => c.title || c.source),
        count
      },
      impact: "Multiple nested queries, slower screen load",
      recommendation:
        "Limit to 1-2 inline collections. Create separate screens " +
        "for additional collections."
    });
  }

  return issues;
}
```

---

### 3. Component Overload Check

```javascript
function analyzeComponentCount(screen) {
  const issues = [];

  const componentCount = screen.components.length;

  if (componentCount >= 30) {
    issues.push({
      severity: "critical",
      category: "layout",
      type: "component-overload",
      location: `${screen.name} screen`,
      description: `${componentCount} components on one screen`,
      details: {
        screenName: screen.name,
        componentCount,
        componentTypes: countComponentTypes(screen.components)
      },
      impact:
        "Very slow screen rendering, cluttered interface, " +
        "overwhelming user experience",
      recommendation:
        "Reduce to 10-15 components. Use Tabs Container to organize " +
        "content into multiple tabs, or split into separate screens."
    });
  } else if (componentCount >= 20) {
    issues.push({
      severity: "warning",
      category: "layout",
      type: "high-component-count",
      location: `${screen.name} screen`,
      description: `${componentCount} components on one screen`,
      details: {
        screenName: screen.name,
        componentCount,
        componentTypes: countComponentTypes(screen.components)
      },
      impact: "Slow screen rendering, cluttered UI",
      recommendation:
        "Consider reducing to 15-20 components. Group related items, " +
        "remove redundant elements, use Tabs Container for organization."
    });
  }

  return issues;
}

function countComponentTypes(components) {
  const counts = {};
  components.forEach(c => {
    counts[c.type] = (counts[c.type] || 0) + 1;
  });
  return counts;
}
```

---

### 4. Table Column Optimization Check

```javascript
function analyzeTableColumns(screen, collection, appData) {
  const issues = [];

  if (collection.type !== "table") return issues;

  // Get visible columns
  const visibleColumns = collection.visibleColumns;

  // Match with table data to get column types
  const table = appData.tables.find(t => t.name === collection.sourceTable);
  if (!table) return issues;

  const expensiveColumns = [];

  visibleColumns.forEach(colName => {
    const column = table.columns.find(c => c.name === colName);
    if (!column) return;

    // Check if expensive
    if (column.computedType === "generate-text" ||
        column.computedType === "image-to-text" ||
        column.computedType === "rollup") {
      expensiveColumns.push({
        name: colName,
        type: column.computedType
      });
    }
  });

  if (expensiveColumns.length > 0) {
    const aiColumns = expensiveColumns.filter(
      c => c.type.includes("text") || c.type === "generate-text"
    );

    if (aiColumns.length > 0) {
      issues.push({
        severity: "warning",
        category: "layout",
        type: "unoptimized-table-view",
        location: `${screen.name} screen (Table collection)`,
        description: `Table showing ${expensiveColumns.length} expensive computed columns`,
        details: {
          screenName: screen.name,
          expensiveColumns,
          aiColumns: aiColumns.length
        },
        impact:
          `AI columns compute for ALL visible rows. ${aiColumns.length} AI columns × ` +
          "50 rows = 100-150 seconds of processing!",
        recommendation:
          "Hide expensive columns from table collection view. Show them only on " +
          "detail screens where they compute once per viewed item."
      });
    } else {
      issues.push({
        severity: "optimization",
        category: "layout",
        type: "table-with-computed-columns",
        location: `${screen.name} screen (Table collection)`,
        description: `Table showing ${expensiveColumns.length} computed columns`,
        details: {
          screenName: screen.name,
          expensiveColumns
        },
        impact: "Moderate performance impact from computed columns in table view",
        recommendation:
          "Consider hiding heavy computed columns from table view. " +
          "Show on detail screen instead."
      });
    }
  }

  return issues;
}
```

---

## Main Analysis Function

```javascript
async function analyzeLayout(appData) {
  const issues = [];
  const screenAnalysis = [];

  // Navigate to Layout Editor
  await navigateToLayoutEditor(appData.appId);

  // Get list of screens
  const screens = await getScreenList();

  // Analyze each screen
  for (const screen of screens) {
    console.log(`Analyzing screen: ${screen.name}...`);

    // Navigate to screen
    await navigateToScreen(screen.name);

    // Wait for components to load
    await waitForComponentList();

    // Get screen data
    const screenData = await extractScreenData();

    // Run analyses
    const componentIssues = analyzeComponentCount(screenData);
    const inlineIssues = analyzeInlineCollections(screenData);

    issues.push(...componentIssues, ...inlineIssues);

    // If screen has collection, analyze it
    if (screenData.collection) {
      const collectionIssues = analyzeCollectionDensity(screenData, screenData.collection);
      const tableIssues = analyzeTableColumns(screenData, screenData.collection, appData);
      issues.push(...collectionIssues, ...tableIssues);
    }

    // Record screen metrics
    screenAnalysis.push({
      screenName: screen.name,
      screenType: screenData.type,
      componentCount: screenData.components.length,
      inlineCollections: screenData.components.filter(c => c.type === "collection" && c.isInline).length,
      collectionFieldCount: screenData.collection?.visibleFields.length || 0,
      issues: componentIssues.length + inlineIssues.length
    });
  }

  // Calculate layout score
  const layoutScore = calculateLayoutScore(issues, screenAnalysis);

  return {
    issues,
    screenAnalysis,
    scores: {
      layoutDesign: layoutScore
    }
  };
}
```

---

## Browser Automation Helpers

### Navigate to Screen

```javascript
async function navigateToScreen(screenName) {
  // In Layout Editor, screens are in navigation section
  // Click the screen/tab
  await browser.click(`text="${screenName}"`);

  // Wait for preview to update
  await browser.waitFor(1000);
}
```

### Extract Screen Data

```javascript
async function extractScreenData() {
  // Take snapshot of component list
  const snapshot = await browser.snapshot("component-list");

  // Parse components from snapshot
  const components = parseComponents(snapshot);

  // Identify collection component
  const collection = components.find(c => c.type.includes("collection"));

  // If collection exists, get its settings
  if (collection) {
    await browser.click(collection.selector);
    await browser.waitFor(500);

    const settingsSnapshot = await browser.snapshot("settings-panel");
    collection.visibleFields = parseVisibleFields(settingsSnapshot);
    collection.visibleColumns = parseVisibleColumns(settingsSnapshot);
  }

  return {
    type: inferScreenType(components),
    components,
    collection
  };
}

function inferScreenType(components) {
  const hasCollection = components.some(c => c.type.includes("collection"));
  const hasTitle = components.some(c => c.type.includes("title"));
  const hasFields = components.some(c => c.type === "fields");

  if (hasCollection && !hasTitle) return "collection";
  if (hasTitle && hasFields) return "detail";
  return "custom";
}
```

---

## Performance Considerations

### Large Apps

Apps with 20+ screens:
- Analysis takes 5-10 minutes
- Provide progress updates
- Consider batching (analyze 5 screens, report progress)

### Browser Timeouts

Layout editor can be slow:
- Increase timeouts to 3-5 seconds
- Wait for component list to fully load
- Retry failed interactions

---

## Error Handling

### Screen Not Accessible

If can't navigate to screen:
- Skip that screen
- Note in analysis: "Screen X not analyzed (navigation failed)"
- Continue with other screens

### Component Data Unavailable

If can't extract component info:
- Skip detailed analysis
- Count components by snapshot visual inspection
- Note limitation in report

---

## Output Format

Return structured results:

```javascript
{
  issues: [
    {
      severity: "warning",
      category: "layout",
      type: "dense-collection",
      location: "Tasks screen",
      description: "Showing 12 fields per card",
      details: { ... },
      impact: "...",
      recommendation: "..."
    }
  ],
  screenAnalysis: [
    {
      screenName: "Tasks",
      screenType: "collection",
      componentCount: 15,
      inlineCollections: 0,
      collectionFieldCount: 12,
      issues: 1
    }
  ],
  scores: {
    layoutDesign: 30
  }
}
```

---

## References

- [Analysis Patterns - Layout Issues](../analysis-patterns.md#layout-anti-patterns)
- [Design Skill](../../design/SKILL.md)
- [Browser Executor](../../../browser/EXECUTOR.md)
