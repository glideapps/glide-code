# Fetch App Data Procedure

Extract app data for audit analysis using browser automation and Glide API.

## ⚠️ CRITICAL: Read-Only Operations

**This procedure must ONLY read data. NEVER modify anything:**
- ✅ Navigate, click to view, take snapshots (READ operations)
- ✅ API GET requests only
- ❌ NEVER use Edit, Write, or Delete tools
- ❌ NEVER make API POST/PATCH/PUT/DELETE requests
- ❌ NEVER click Save, Update, or Confirm buttons
- ❌ NEVER modify column settings or app structure

## Overview

This procedure handles the data extraction phase of the audit:
1. Parse app URL to get App ID
2. Navigate to app in browser
3. Extract API token via browser automation
4. Fetch tables and schemas via API (GET requests only)
5. Return structured data for analysis

## Prerequisites

- User has provided a Glide app URL
- Browser is authenticated to Glide (user logged in)
- Browser-1 is available for automation

## Input

**Glide App URL** in one of these formats:
- `https://go.glideapps.com/app/abc123def456`
- `go.glideapps.com/app/abc123def456`
- `abc123def456` (just the App ID)

## Output

Structured data object containing:
```javascript
{
  appId: "abc123def456",
  appName: "My App Name",
  apiToken: "glide_api_xxx...xxx",
  tables: [
    {
      tableID: "table123",
      name: "Tasks",
      rowCount: 150,
      columns: [
        {
          id: "col1",
          name: "Title",
          type: "string",
          computedType: null
        },
        {
          id: "col2",
          name: "Status Display",
          type: "computed",
          computedType: "if-then-else",
          dependencies: ["col3", "col4"]
        }
        // ... more columns
      ]
    }
    // ... more tables
  ]
}
```

## Procedure Steps

### Step 1: Parse App URL

Extract App ID from the provided URL.

**Regex patterns**:
```javascript
// Full URL
const pattern1 = /go\.glideapps\.com\/app\/([a-zA-Z0-9]+)/;

// If just ID provided
const pattern2 = /^[a-zA-Z0-9]{12,}$/;
```

**Extraction**:
```javascript
function extractAppId(input) {
  // Try full URL pattern
  const match1 = input.match(/go\.glideapps\.com\/app\/([a-zA-Z0-9]+)/);
  if (match1) return match1[1];

  // Try just ID pattern
  if (/^[a-zA-Z0-9]{12,}$/.test(input)) return input;

  // Invalid format
  throw new Error("Invalid Glide app URL or ID");
}

const appId = extractAppId(userInput);
```

**Validation**:
- App ID should be 12+ alphanumeric characters
- If extraction fails, report error to user

---

### Step 2: Navigate to Data Editor

Use browser automation to open the app's Data Editor.

**Target URL**:
```
https://go.glideapps.com/app/{appId}/data
```

**Browser commands**:
```
1. NAVIGATE to https://go.glideapps.com/app/{appId}/data
2. WAIT for page load (selector: Data Editor UI elements)
3. VERIFY login status (if logged out, prompt user to log in)
```

**Expected state**:
- Data Editor is loaded
- Can see table list in left sidebar
- User is authenticated

---

### Step 3: Extract API Token

Use the existing `get-api-key` procedure to extract the API token.

**Reference**: `glide/agents/data/procedures/get-api-key.md`

**Summary of steps** (see full procedure for details):

1. **Select a table** (e.g., Users table or any available table)
2. **Click "Show API" button**
3. **Locate the API token** in the panel:
   - Look for "Bearer {token}" pattern
   - Or find copy button for token
4. **Extract token value**
5. **Close API panel**

**Store token**:
```javascript
const apiToken = extractedToken; // Store for API calls
```

**Validation**:
- Token should be a long alphanumeric string
- Test with API call to `/tables` endpoint

---

### Step 4: Fetch App Name (Optional)

Extract app name from page title or UI.

**Browser command**:
```
SNAPSHOT of page title or app header
Extract app name
```

**Fallback**:
- If can't extract, use "Unnamed App" or leave empty
- App name is for reporting only, not critical

---

### Step 5: Fetch Tables via API

Use Glide API v2 to get all tables.

**API Call**:
```bash
GET https://api.glideapps.com/tables
Authorization: Bearer {apiToken}
Content-Type: application/json
```

**Response**:
```json
{
  "data": [
    {
      "id": "table123",
      "name": "Tasks",
      "rowCount": 150
    },
    {
      "id": "table456",
      "name": "Projects",
      "rowCount": 25
    }
  ]
}
```

**Implementation**:
```javascript
async function fetchTables(apiToken) {
  const response = await fetch('https://api.glideapps.com/tables', {
    method: 'GET',
    headers: {
      'Authorization': `Bearer ${apiToken}`,
      'Content-Type': 'application/json'
    }
  });

  if (!response.ok) {
    throw new Error(`API error: ${response.status}`);
  }

  const data = await response.json();
  return data.data; // Array of tables
}
```

---

### Step 6: Fetch Schema for Each Table

For each table, get detailed column information.

**Note**: Glide API v2 doesn't expose full schema with column types directly. We need to use browser automation to inspect the Data Editor.

**Hybrid approach**:

#### Option A: Browser Inspection (Detailed)

For each table:

1. **Navigate to table** in Data Editor:
   ```
   CLICK table in sidebar
   WAIT for table to load
   ```

2. **Get column headers**:
   ```
   SNAPSHOT of table header row
   Extract column names
   ```

3. **Inspect each column**:
   ```
   For each column:
     CLICK column header
     CLICK "Edit column" or settings
     SNAPSHOT column configuration panel
     Extract:
       - Column name
       - Column type (Text, Number, Math, If-Then-Else, etc.)
       - For computed columns: get formula/configuration
       - For relations: get target table
       - For rollups: get source (relation or table?)
     CLOSE configuration panel
   ```

4. **Build column dependency graph**:
   - For computed columns, identify which columns they reference
   - Create edges in dependency graph

#### Option B: API with Limited Data (Faster)

Use API to get basic row data, infer column types:

**API Call**:
```bash
GET https://api.glideapps.com/tables/{tableID}/rows?limit=1
Authorization: Bearer {apiToken}
```

**Response gives**:
```json
{
  "data": [
    {
      "id": "row1",
      "name": "Task 1",
      "status": "Active",
      "dueDate": "2026-01-15T00:00:00Z"
    }
  ]
}
```

**Infer types**:
- String values → Text column
- Number values → Number column
- ISO dates → Date column
- Cannot distinguish computed vs basic columns

**Limitation**: Can't detect computed columns, dependencies, or relations with API alone.

**Recommendation for audit**: Use **Option A (Browser Inspection)** for detailed analysis, fall back to **Option B** if browser automation fails.

---

### Step 7: Build Column Dependency Graph

For computed columns, identify dependencies.

**Example**:
```
Tasks table:
- Due Date (basic) → no dependencies
- Days Until Due (Math: Due Date - Today) → depends on [Due Date]
- Status Display (If-Then-Else: based on Days Until Due) → depends on [Days Until Due]

Graph:
Due Date → Days Until Due → Status Display
```

**Data structure**:
```javascript
const columnGraph = {
  "Due Date": {
    type: "basic",
    dependencies: []
  },
  "Days Until Due": {
    type: "computed",
    computedType: "math",
    dependencies: ["Due Date"]
  },
  "Status Display": {
    type: "computed",
    computedType: "if-then-else",
    dependencies: ["Days Until Due"]
  }
};
```

**Build graph**:
1. Parse column formulas to extract referenced columns
2. For Math columns: extract variable names from formula
3. For If-Then-Else: extract columns used in conditions
4. For Lookups: identify relation column
5. For Rollups: identify relation column (if any)

---

### Step 8: Compile and Return Data

Package all extracted data into structured format.

**Final data object**:
```javascript
const appData = {
  appId: "abc123def456",
  appName: "Task Manager",
  apiToken: "glide_api_xxx...xxx",
  auditDate: new Date().toISOString(),
  tables: tables.map(table => ({
    tableID: table.id,
    name: table.name,
    rowCount: table.rowCount,
    columns: extractedColumns[table.id], // From browser inspection
    relations: extractedRelations[table.id],
    ai Columns: countAIColumns[table.id],
    computedColumns: countComputedColumns[table.id]
  }))
};

return appData;
```

---

## Error Handling

### API Token Extraction Fails

**Symptoms**:
- Can't find "Show API" button
- Token not visible in panel
- Copy fails

**Recovery**:
1. Refresh Data Editor page
2. Try different table
3. Check if user has API access (team permissions)
4. Report error to user with instructions

### API Calls Fail (401/403)

**Symptoms**:
- 401 Unauthorized
- 403 Forbidden

**Recovery**:
1. Verify token is correct (not truncated)
2. Check if this is a Big Table app (API v2 only works with Big Tables)
3. Fall back to browser-only analysis (skip API data)
4. Report limited analysis to user

### Browser Automation Fails

**Symptoms**:
- Can't locate elements
- Page structure changed
- Timeout errors

**Recovery**:
1. Take snapshot, describe what's visible
2. Try alternative selectors
3. Skip detailed column analysis if necessary
4. Proceed with API-only data (limited)

### App Not Found (404)

**Symptoms**:
- App URL doesn't load
- 404 error

**Recovery**:
1. Verify App ID is correct
2. Check if user has access to this app
3. Report error to user

---

## Performance Considerations

### Large Apps (50+ Tables)

For apps with many tables:
- Fetching all table schemas can take 5-10 minutes
- Consider progress updates to user
- Implement batching (analyze 10 tables at a time)

**Progress reporting**:
```
Analyzing app structure...
- Extracted API token ✓
- Found 57 tables
- Analyzing tables: 10/57 (18%)
- Analyzing tables: 20/57 (35%)
...
```

### API Rate Limiting

Glide API may have rate limits:
- Throttle requests (1 per second)
- Batch operations where possible
- Cache results

### Browser Timeout

Data Editor may be slow for large tables:
- Increase timeouts for table loading
- Wait for column panels to fully load
- Add retries for failed interactions

---

## Example Execution

```javascript
// User provides URL
const userInput = "https://go.glideapps.com/app/abc123def456";

// Step 1: Parse
const appId = extractAppId(userInput);
console.log(`App ID: ${appId}`);

// Step 2-3: Browser automation
await navigateToDataEditor(appId);
const apiToken = await extractAPIToken();
console.log(`API Token: ${apiToken.substring(0, 10)}...`);

// Step 5: Fetch tables
const tables = await fetchTables(apiToken);
console.log(`Found ${tables.length} tables`);

// Step 6-7: Inspect columns
const detailedTables = [];
for (const table of tables) {
  console.log(`Analyzing table: ${table.name}...`);
  const columns = await inspectTableColumns(table.id);
  const graph = buildDependencyGraph(columns);
  detailedTables.push({
    ...table,
    columns,
    dependencyGraph: graph
  });
}

// Step 8: Return
return {
  appId,
  apiToken,
  tables: detailedTables
};
```

---

## Output for Next Procedure

The extracted data is passed to:
- **analyze-data-structure.md** - For performance analysis
- **analyze-layout.md** - For UI analysis (needs browser still open)

---

## References

- [Get API Key Procedure](../../../agents/data/procedures/get-api-key.md)
- [Glide API v2 Documentation](https://apidocs.glideapps.com/api-reference/v2/general/introduction)
- [Data Modeling Skill](../../data-modeling/SKILL.md)
