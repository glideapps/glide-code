---
name: api
description: |
  Use the Glide API for programmatic data operations - REST API v2 and @glideapps/tables npm package.
  Use when importing data, bulk operations, automating data sync, or building integrations with Glide tables.
---

# Glide API

## API Overview

### Two Ways to Access Data

1. **REST API v2** - Direct HTTP calls to `api.glideapps.com`
2. **@glideapps/tables** - Official npm package (simpler for JavaScript/TypeScript)

### Important Limitations

- **Big Tables Only**: API v2 only works with Glide Big Tables, not native app tables
- **Team Scope**: API operates at team level, not app level
- **Quota Costs**: Read/write operations consume updates from your plan
- **Row Limits**: Big Tables support up to 10 million rows
- **Aggregation Limits**: Rollups/Lookups limited to 100 matching rows in Big Tables
- **Computed Column Limits**: Some computed column types can't be filtered/sorted (see below)

## Getting Your API Token

1. Open any app in Glide Builder
2. Go to **Data** tab
3. Click **Show API** button (bottom of data grid)
4. Click **Copy secret token**

Store token securely - never commit to version control.

## REST API v2

### Base URL
```
https://api.glideapps.com/
```

### Authentication
```
Authorization: Bearer YOUR_API_TOKEN
```

### Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/tables` | List all Big Tables |
| POST | `/tables` | Create new Big Table |
| GET | `/tables/{tableID}/rows` | Get rows |
| GET | `/tables/{tableID}/rows/{rowID}` | Get single row |
| HEAD | `/tables/{tableID}/rows` | Get table version |
| POST | `/tables/{tableID}/rows` | Add rows |
| PATCH | `/tables/{tableID}/rows/{rowID}` | Update row |
| PUT | `/tables/{tableID}` | Overwrite table |
| DELETE | `/tables/{tableID}/rows/{rowID}` | Delete row |

### Example: List Tables

```bash
curl -X GET "https://api.glideapps.com/tables" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json"
```

### Example: Get Rows

```bash
curl -X GET "https://api.glideapps.com/tables/TABLE_ID/rows" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json"
```

### Example: Create Table

Create a new Big Table with schema and initial data. See [official docs](https://apidocs.glideapps.com/api-reference/v2/tables/post-tables).

```bash
curl -X POST "https://api.glideapps.com/tables" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Contacts",
    "appsToLink": ["APP_ID_HERE"],
    "schema": {
      "columns": [
        {"id": "fullName", "displayName": "Full Name", "type": "string"},
        {"id": "email", "displayName": "Email", "type": "string"},
        {"id": "photo", "displayName": "Photo", "type": "uri"},
        {"id": "createdAt", "displayName": "Created", "type": "dateTime"}
      ]
    },
    "rows": [
      {
        "fullName": "Alex Bard",
        "email": "alex@example.com",
        "photo": "https://randomuser.me/api/portraits/men/32.jpg",
        "createdAt": "2024-07-29T14:04:15.561Z"
      }
    ]
  }'
```

**Response:**
```json
{
  "data": {
    "tableID": "2a1bad8b-cf7c-44437-b8c1-e3782df6",
    "rowIDs": ["zcJWnyI8Tbam21V34K8MNA"],
    "linkedAppIDs": ["APP_ID_HERE"]
  }
}
```

**Column Types for Schema:**
| Type | Description |
|------|-------------|
| `string` | Text data |
| `number` | Numeric values |
| `dateTime` | ISO 8601 dates |
| `uri` | URLs (images, links) |
| `boolean` | True/false |

**Key Points:**
- `appsToLink`: Array of app IDs to automatically link the table to
- `schema.columns`: Each has `id` (used in row data), `displayName` (shown in UI), `type`
- `rows`: Use column `id` values as keys, NOT `displayName`

### Example: Add Rows

**Use column IDs (not display names) as keys.** Column IDs are defined in the schema when creating the table.

```bash
curl -X POST "https://api.glideapps.com/tables/TABLE_ID/rows" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "rows": [
      {"fullName": "Alice", "email": "alice@example.com"},
      {"fullName": "Bob", "email": "bob@example.com"}
    ]
  }'
```

### Example: Update Row

```bash
curl -X PATCH "https://api.glideapps.com/tables/TABLE_ID/rows/ROW_ID" \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Updated Name"
  }'
```

### Example: Delete Row

```bash
curl -X DELETE "https://api.glideapps.com/tables/TABLE_ID/rows/ROW_ID" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

## @glideapps/tables Package

### Installation

```bash
npm install @glideapps/tables
```

### Setup

```javascript
import * as glide from "@glideapps/tables";

const myTable = glide.table({
  token: "YOUR_API_TOKEN",
  app: "APP_ID",           // Optional for Big Tables
  table: "TABLE_ID",
  columns: {
    name: { type: "string", name: "Name" },
    email: { type: "email-address", name: "Email" },
    photo: { type: "image-uri", name: "Photo" },
    active: { type: "boolean", name: "Active" }
  }
});
```

### Column Types

| Type | Description |
|------|-------------|
| `string` | Text |
| `number` | Numeric |
| `boolean` | True/False |
| `date-time` | Date and time |
| `image-uri` | Image URL |
| `email-address` | Email |
| `phone-number` | Phone |
| `uri` | URL |

### Get Rows

```javascript
// Get all rows
const rows = await myTable.get();

// With query parameters
const filtered = await myTable.get({
  where: { active: true },
  limit: 10
});
```

### Add Rows

```javascript
// Add single row
await myTable.add({
  name: "Alice",
  email: "alice@example.com"
});

// Add multiple rows
await myTable.add([
  { name: "Alice", email: "alice@example.com" },
  { name: "Bob", email: "bob@example.com" }
]);
```

### Update Row

```javascript
await myTable.update(rowId, {
  name: "Updated Name"
});
```

### Delete Row

```javascript
await myTable.delete(rowId);
```

## Data Versioning

The API supports optimistic locking via ETags:

```bash
# Get current version
HEAD /tables/{tableID}/rows

# Update with version check
PATCH /tables/{tableID}/rows/{rowID}
If-Match: "version-etag"
```

If the version has changed, you'll get HTTP 412 Precondition Failed.

## Pricing

| Operation | Cost |
|-----------|------|
| Write (per row) | 0.01 updates |
| Read (per row) | 0.001 updates |
| List tables | Free |
| Get version | Free |

Additional updates beyond quota: $0.02 each

## Big Table Computed Column Limits

When querying Big Tables, not all computed columns can be used for filtering or sorting.

**Supported for filtering/sorting:**
- Math columns
- If-Then-Else columns
- Lookup columns (single relation, basic columns only)
- Template columns (static template only)

**NOT supported for filtering/sorting:**
- Rollup columns
- Multi-relation columns
- Query columns
- Plugin-based columns

**Lookup requirements:**
- Must use single relation (not multi-relation)
- Relation column must be basic (non-computed)
- Target table must be a Big Table
- Target column must be basic and not user-specific

**Aggregation limit:** Rollups/Lookups return max 100 matching rows.

See the `data-modeling` skill for full Big Table documentation.

## Best Practices

1. **Use Big Tables** for API access - native tables aren't supported
2. **Batch operations** - Add multiple rows in one call
3. **Use versioning** - Prevent data conflicts with If-Match
4. **Cache tokens** - Don't request new tokens repeatedly
5. **Handle errors** - Check for 4xx/5xx responses
6. **Respect rate limits** - Don't hammer the API
7. **Check computed column support** - Not all computed columns work with filters

## Common Patterns

### Sync External Data to Glide

```javascript
import * as glide from "@glideapps/tables";

const targetTable = glide.table({
  token: process.env.GLIDE_TOKEN,
  table: "my-big-table-id",
  columns: {
    externalId: { type: "string", name: "External ID" },
    name: { type: "string", name: "Name" },
    updatedAt: { type: "date-time", name: "Updated At" }
  }
});

async function syncData(externalData) {
  // Get existing rows
  const existing = await targetTable.get();
  const existingMap = new Map(existing.map(r => [r.externalId, r]));

  // Process updates and inserts
  const toAdd = [];
  for (const item of externalData) {
    if (!existingMap.has(item.id)) {
      toAdd.push({
        externalId: item.id,
        name: item.name,
        updatedAt: new Date().toISOString()
      });
    }
  }

  if (toAdd.length > 0) {
    await targetTable.add(toAdd);
  }
}
```

### Bulk Import from CSV

```javascript
import * as glide from "@glideapps/tables";
import { parse } from "csv-parse/sync";
import fs from "fs";

const table = glide.table({
  token: process.env.GLIDE_TOKEN,
  table: "target-table-id",
  columns: {
    col1: { type: "string", name: "Column 1" },
    col2: { type: "string", name: "Column 2" }
  }
});

const csvContent = fs.readFileSync("data.csv", "utf-8");
const records = parse(csvContent, { columns: true });

// Batch in chunks of 100
const chunkSize = 100;
for (let i = 0; i < records.length; i += chunkSize) {
  const chunk = records.slice(i, i + chunkSize);
  await table.add(chunk.map(r => ({
    col1: r["Column 1"],
    col2: r["Column 2"]
  })));
}
```

## Documentation

- [Glide API Reference](https://apidocs.glideapps.com/api-reference/v2/general/introduction)
- [Tables API](https://apidocs.glideapps.com/api-reference/v2/tables)
- [@glideapps/tables npm](https://www.npmjs.com/package/@glideapps/tables)
