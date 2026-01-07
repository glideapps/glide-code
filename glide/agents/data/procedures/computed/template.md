---
name: template-column
description: Add a Template column to combine text and column values
preconditions:
  - Table exists with source columns
  - Must be in Data Editor
---

# Template Column Procedure

Combines text and column values into formatted strings. Perfect for display labels, URLs, and concatenated fields.

## Use Cases

- Full name: `{FirstName} {LastName}`
- Display labels: `{ProductName} - ${Price}`
- Dynamic URLs: `https://example.com/user/{UserId}`
- Formatted addresses: `{City}, {State} {ZipCode}`
- Rich labels: `{Name} ({Category})`

## Syntax

| Pattern | Result |
|---------|--------|
| `{ColumnName}` | Inserts column value |
| Plain text | Literal text |
| `{Col1} {Col2}` | Multiple columns with space |
| `{Col} - text` | Mixed columns and text |

---

## Procedure

### Step 1: Navigate to Table

```
COMMAND: NAVIGATE
URL: https://go.glideapps.com/app/{appId}/data
```

```
COMMAND: SNAPSHOT
PURPOSE: Find and select the target table
```

```
COMMAND: CLICK
REF: {ref of table in sidebar}
ELEMENT: "{tableName}" in table list
```

### Step 2: Open Add Column

```
COMMAND: SNAPSHOT
PURPOSE: Find add column button
```

```
COMMAND: CLICK
REF: {ref of add column button}
ELEMENT: Add column button (+ icon in header)

EXPECTED: Column creation panel opens
```

### Step 3: Enter Column Name

```
COMMAND: TYPE
REF: {ref of name input}
ELEMENT: Column name input
TEXT: {columnName}

EXAMPLES: "Full Name", "Display Title", "Image URL"
```

### Step 4: Select Template Type

```
COMMAND: CLICK
REF: {ref of type dropdown}
ELEMENT: Type dropdown
```

```
COMMAND: SNAPSHOT
PURPOSE: Find computed columns
```

```
COMMAND: CLICK
REF: {ref of Computed Columns section}
ELEMENT: "Computed Columns" category
```

```
COMMAND: SNAPSHOT
```

```
COMMAND: CLICK
REF: {ref of Template option}
ELEMENT: "Template Column" option

EXPECTED: Template editor appears
```

### Step 5: Build Template

```
COMMAND: SNAPSHOT
PURPOSE: Find template input area

FIND:
  - Text area for entering template
  - List of available columns to insert
  - Preview of result
```

**Option A: Type template directly**

```
COMMAND: CLICK
REF: {ref of template input}
ELEMENT: Template input area
```

```
COMMAND: TYPE
REF: {ref of template input}
ELEMENT: Template input
TEXT: {template}

SYNTAX: {ColumnName} inserts column value

EXAMPLES:
  {FirstName} {LastName}
  {ProductName} - ${Price}
  Order #{OrderNumber}
  {City}, {State} {ZipCode}
  https://picsum.photos/seed/{ProductId}/400/300
```

**Option B: Use column chips (more reliable)**

```
COMMAND: SNAPSHOT
PURPOSE: Find column insertion UI

FIND: 
  - Column chips or buttons to click
  - "Insert column" option
  - Column names in a list
```

For each column to insert:

```
COMMAND: CLICK
REF: {ref of column chip}
ELEMENT: "{columnName}" column

EXPECTED: {ColumnName} inserted into template
```

Type any literal text between column insertions.

### Step 6: Verify Preview

```
COMMAND: SNAPSHOT
PURPOSE: Check template preview

VERIFY:
  - Column values are being replaced
  - Formatting looks correct
  - No broken {references}
  - Spacing and punctuation correct
```

### Step 7: Save Column

```
COMMAND: CLICK
REF: {ref of save button}
ELEMENT: Save button

EXPECTED: Template column added with merged values
```

---

## Common Templates

### People

| Purpose | Template |
|---------|----------|
| Full Name | `{FirstName} {LastName}` |
| Name + Title | `{Name}, {JobTitle}` |
| Name + Company | `{Name} ({Company})` |
| Email Display | `{Name} <{Email}>` |

### Products/Items

| Purpose | Template |
|---------|----------|
| Product Label | `{Name} - ${Price}` |
| SKU Label | `{SKU}: {ProductName}` |
| Inventory | `{Name} (Qty: {Stock})` |

### Locations

| Purpose | Template |
|---------|----------|
| Full Address | `{Street}, {City}, {State} {Zip}` |
| City/State | `{City}, {State}` |
| Location Name | `{Name} - {City}` |

### URLs (Dynamic Images)

| Purpose | Template |
|---------|----------|
| Picsum (random) | `https://picsum.photos/seed/{Id}/400/300` |
| Gravatar | `https://gravatar.com/avatar/{EmailHash}` |
| Custom API | `https://api.example.com/image/{ItemId}` |

### Display Labels

| Purpose | Template |
|---------|----------|
| Status Badge | `{StatusEmoji} {StatusText}` |
| Date Label | `Due: {DueDate}` |
| Count Label | `{Count} items` |
| Reference | `#{ReferenceNumber}` |

---

## Error Recovery

### {ColumnName} Shows Literally

```
ISSUE: Template shows "{FirstName}" instead of "John"

CHECK:
  - Column name spelled exactly right?
  - Case matches? ({firstName} vs {FirstName})
  - Column exists in this table?
  - Curly braces correct? Not (FirstName) or [FirstName]

FIX: Use column picker instead of typing
```

### Empty Results

```
ISSUE: Template produces empty string

CHECK:
  - Do source columns have data?
  - Are all referenced columns populated?

NOTE: If any column is empty, that part will be blank
TIP: Use If-Then-Else to handle empty cases first
```

### Special Characters Breaking Template

```
ISSUE: Template not working with special characters

CHECK:
  - Column names with spaces? Use exact name including spaces
  - Columns with special chars? Might need escaping

FIX: Usually the column picker handles this automatically
```

---

## Tips

1. **Use column picker when available** - Avoids typos in column names
2. **Check the preview** - Make sure values substitute correctly before saving
3. **Handle empty values** - Template will have gaps if columns are empty
4. **Watch spacing** - `{First} {Last}` has space, `{First}{Last}` doesn't
5. **For URLs** - Use unique IDs in seeds for different images per row

### Image URL Best Practices

For generating unique images per row:

```
✅ Good: https://picsum.photos/seed/{ProductId}/400/300
   Each product gets a different image based on its ID

❌ Bad: https://picsum.photos/400/300
   Same random image for all rows
```

For people avatars:

```
https://randomuser.me/api/portraits/{gender}/{number}.jpg
https://i.pravatar.cc/300?u={UserId}
```

---

## Report Format

```
TEMPLATE COLUMN ADDED:
  Table: {tableName}
  Column: {columnName}
  Template: {the template pattern}
  Status: SUCCESS | FAILED
  Sample Output: "{example merged value}"
```
