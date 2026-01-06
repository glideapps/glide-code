---
name: data-modeling
description: |
  Design Glide data models - tables, columns, column types, and calculation architecture.
  Use when creating tables, designing data structures, choosing column types, or planning relationships.
---

# Glide Data Modeling

## Table Creation Strategy

**When building apps, create tables via the Glide API, not the UI.**

If you're starting a new app or adding tables to an existing app, the workflow is:

1. **Design the data schema**
   - Determine what tables you need
   - Define columns for each table (names, types)
   - Plan relationships between tables
   - Consider what sample data would demonstrate the structure

2. **Use the data agent to create tables**
   - Pass the schema to the data agent
   - Include sample data to populate the tables
   - The data agent uses the Glide API to create tables
   - Tables are automatically linked to your app

3. **Don't try to create tables in the UI**
   - The API is much faster for bulk creation
   - API ensures consistent structure
   - Sample data validates the schema immediately

**This is the primary table creation method.** Only add columns via the UI after tables exist (see "Creating Columns via UI" section below).

## Key Concept: Column-Based Calculations

**IMPORTANT**: All calculations in Glide are built in the Data Editor by adding computed columns that build off one another. Unlike spreadsheets with cell formulas, Glide uses a column-based computation model.

To build complex calculations:
1. Create intermediate computed columns
2. Chain columns together (one column references another)
3. Build up to your final result step by step

Example: Calculate total with tax
1. Column: `Subtotal` (Math: Price * Quantity)
2. Column: `Tax` (Math: Subtotal * 0.08)
3. Column: `Total` (Math: Subtotal + Tax)

## Column Types Overview

### Basic Columns (Data Storage)

| Type | Description | Use Case |
|------|-------------|----------|
| **Text** | String data | Names, descriptions |
| **Number** | Numeric values | Prices, quantities |
| **Boolean** | True/False | Flags, toggles |
| **Image** | Image URL | Profile photos, product images |
| **Date & Time** | Timestamps | Due dates, created at |
| **URL** | Web links | External resources |
| **Row ID** | Unique identifier | Auto-generated ID |
| **Rich Text** | Formatted text | Long descriptions |
| **Email** | Email addresses | Contact info |
| **Phone Number** | Phone format | Contact info |
| **Duration** | Time duration | Task duration |
| **Emoji** | Emoji picker | Reactions, status |
| **Multiple files** | File array | Attachments |
| **Multiple images** | Image array | Photo galleries |

### Computed Columns (Calculations)

| Type | Description | Use Case |
|------|-------------|----------|
| **Math** | Arithmetic calculations | Totals, percentages |
| **Template** | Text concatenation | Display names, labels |
| **If-Then-Else** | Conditional logic | Status labels, flags |
| **Query** | Query other tables | Cross-table data |
| **Relation** | Link rows between tables | Relationships |
| **Lookup** | Get values via relation | Related data |
| **Rollup** | Aggregate related data | Sums, counts, averages |
| **Single Value** | One value from relation | First/last related item |
| **Joined List** | Array to text | Comma-separated list |
| **Split Text** | Text to array | Parse delimited data |
| **Make Array** | Create array | Multiple values |
| **Distance** | Geographic distance | Location calculations |
| **Generate Image** | Create images | Dynamic graphics |
| **Construct URL** | Build URLs | Dynamic links |

### AI Columns (Powerful!)

Glide AI columns run inference on your data automatically. **Look for opportunities to add these to make apps more useful.**

| Type | Description | Use Case |
|------|-------------|----------|
| **Generate Text** | AI text generation | Summaries, descriptions, responses, recommendations |
| **Image to Text** | Extract info from images | Receipt scanning, document OCR, image analysis |
| **Document to Text** | Extract text from docs | PDF parsing, document summarization |
| **Audio to Text** | Transcribe audio | Voice notes, meeting recordings |
| **Text to Boolean** | AI classification | Sentiment analysis, spam detection, yes/no questions |
| **Text to Choice** | AI categorization | Auto-tagging, priority assignment, category detection |
| **Text to JSON** | Extract structured data | Form parsing, entity extraction |
| **Text to Number** | Extract numbers | Data extraction from text |
| **Text to Date** | Parse dates | Natural language date parsing |
| **Text to Texts** | Split into multiple | List extraction, keyword extraction |

#### AI Column Ideas by App Type

| App Type | AI Enhancement Ideas |
|----------|---------------------|
| **Task Management** | Auto-prioritize tasks, summarize long descriptions, extract due dates from text |
| **Inventory** | Generate product descriptions, extract info from product images |
| **CRM** | Summarize customer notes, sentiment analysis on feedback, auto-categorize leads |
| **Content/Documents** | Summarize documents, extract key points, auto-tag content |
| **Support/Tickets** | Auto-categorize issues, suggest responses, priority detection |
| **Expense Tracking** | Extract data from receipt photos, categorize expenses |

### Integration Columns

| Category | Columns |
|----------|---------|
| **AI Services** | Claude, OpenAI, Google Gemini, OpenRouter, Replicate |
| **Google** | Google Cloud, Cloud Vision, Google Maps |
| **Data** | Call API, JSON, CSV, XML |
| **Utilities** | Construct URL, Run Javascript Code |
| **Services** | Clearbit, Giphy, Gravatar, Hubspot, Pexels, Short.io, Yelp, ZenRows, OpenGraph.io, Radar |
| **App** | App special values, Browser, Device Info, Data Structures |

## Creating Columns via UI

### Adding a Column (Keyboard Shortcut - Recommended)

**Fastest method**: Use keyboard shortcut with browser automation

1. Navigate to the Data Editor (Data tab)
2. Click on the table where you want to add a column
3. Press **CMD+SHIFT+ENTER** (macOS) or **CTRL+SHIFT+ENTER** (Windows)
4. Type the column **Name**
5. **Select the Type** from dropdown (required - commits the name)
6. Configure type-specific options
7. Click **Save**

**Why this method:**
- Much faster than clicking through UI menus
- Works reliably with browser automation
- Direct keyboard access to column creation

### Adding a Column (Manual Method)

Alternative UI-based approach:

1. Go to Data Editor (Data tab)
2. Click any column header
3. Select "Add column right"
4. Configure:
   - **Name**: Column name
   - **Type**: Select from dropdown (searchable)
   - Type-specific options
5. Click Save

**Note**: Keyboard shortcut method is preferred for automation workflows.

### Column Type Picker
The Type dropdown is organized into categories:
- **Basic** - Storage types
- **Computed** - Calculation types
- **AI** - AI-powered processing
- **Integrations** - External services

Type in the search box to filter.

## Row IDs - The Foundation of Relations

**Every table should have a Row ID column.** Row IDs are unique identifiers that Glide auto-generates for each row, and they're the proper way to create reliable relations between tables.

### Creating a Row ID Column

1. In Data Editor, click any column header → "Add column right" (or use **CMD+SHIFT+ENTER**)
2. Name the column (or leave blank - it will auto-name to "Row ID")
3. Change Type to **Basic → Row ID**
4. The column auto-names itself "Row ID"
5. Glide automatically generates unique IDs for every row

### Why Use Row IDs for Relations

**Row IDs are better than names or other fields** for relations because:
- **Unique**: Guaranteed to be unique (names can duplicate)
- **Stable**: Never change even if other data is updated
- **Reliable**: Work correctly with Glide's relation system
- **Standard**: The proper Glide pattern for table relationships

### Common Pattern: Data Contains Row IDs

Often your imported data or form submissions will already contain Row IDs as references:

**Example**: Visits table with client references
- Visits table has a "Client" column containing Row IDs (e.g., "abc123")
- These are Row IDs from the Clients table, not client names
- Create a relation matching Visits → Client to Clients → Row ID
- Use Lookup columns to display actual client information

This pattern is common when:
- Forms submit data and store user Row IDs
- One table references another via Row ID
- Data imported from systems using unique IDs

## Per-Item Aggregations: Query Pattern

**Critical Concept**: When you need to count, sum, or aggregate data PER ROW (not for the whole table), use Query columns to filter first, then Rollup/Single Value.

### The Problem: Rollup on Entire Table

**Common Mistake:**
- You want "Total Visits" for each client
- You create a Rollup that counts the entire Visits table
- **Result**: Every client shows the same number (total visits across ALL clients)
- This happens because you're aggregating the whole table, not per-client

### The Solution: Query → Rollup Pattern

**Correct Approach:**

```
1. Query Column: Filter the data to THIS item
   - Source: The table you want to aggregate (e.g., Visits)
   - Filter: Match to this row (e.g., Client matches Row ID)
   - Sort: Optional, for ordered results

2. Rollup/Single Value Column: Aggregate the filtered Query
   - Source: The Query column (NOT the whole table)
   - Aggregation: Count, Sum, Average, etc.
   - Result: Per-item aggregation
```

**Example: Client Visit Statistics**

In Clients table:
1. **Query**: "Client's Visits" → Visits where Client = Row ID, sorted by date descending
2. **Rollup**: "Total Visits" → Count of Client's Visits Query
3. **Single Value**: "Last Visit Date" → First item of Client's Visits → Visit Date

**Now each client shows their own stats:**
- Sarah Johnson: 1 visit
- Maria Rodriguez: 2 visits
- etc.

### Query vs Relation for Aggregations

**Use Query when:**
- You need per-item counts, sums, or averages
- You need filtering beyond simple column matching
- You need sorted results (e.g., most recent first)

**Use Relation when:**
- Simple one-to-one or one-to-many lookups
- Standard Lookup pattern (get related values)
- No complex filtering needed

**Key insight from expert:** "Query and relations are basically the same thing, except queries let you add more filters."

## Relation & Lookup Pattern

This is the most common pattern for connecting data:

### Step 0: Ensure Both Tables Have Row ID Columns (Recommended)
- Add a Row ID column to both tables if they don't already have one
- Type: Basic → Row ID
- This creates a stable, unique identifier for each row

### Step 1: Create Relation Column
- Creates a link between two tables
- **Best practice**: Match using Row ID columns
- Match rows based on a key column (preferably Row ID)
- Can be single or multiple relations

**Example**: Link Visits to Clients
- Visits table has "Client" column (contains Row IDs)
- Clients table has "Row ID" column
- Create Relation in Visits: Match "Client" to "Clients → Row ID"

### Step 2: Create Lookup Column
- References the Relation column
- Pulls specific column values from related rows
- Example: Lookup "Client Name" and "Client Phone" via the Client relation
- This brings human-readable data into your table

### Step 3: Create Rollup Column (if aggregating)
- References the Relation column
- Aggregates values: Count, Sum, Average, Max, Min, etc.
- Example: Count of tasks assigned to employee

## Math Column Syntax

Math columns support:
- Basic arithmetic: `+`, `-`, `*`, `/`
- Column references: Click to insert column name
- Functions: Check Glide docs for available functions
- Parentheses for order of operations

Example: `(Price * Quantity) * (1 + TaxRate)`

## If-Then-Else Pattern

Structure:
```
IF [condition]
THEN [value if true]
ELSE [value if false]
```

Can be nested for multiple conditions:
```
IF Status = "Overdue"
THEN "!"
ELSE IF Status = "Due Today"
THEN "Today"
ELSE ""
```

## Template Column Syntax

Templates use replacement syntax:
```
Hello, {Name}! You have {TaskCount} tasks.
```

Reference any column by wrapping its name in curly braces.

## Glide Big Tables vs Native Tables

**Native Tables (Glide Tables)**
- Built into every app
- Good for small-medium data
- Full computed column support
- Cannot be shared between apps

**Big Tables**
- Separate from apps (team-level)
- Handles large datasets
- Required for API v2 access
- **Can be shared across multiple apps**

## Sharing Data Between Apps (Big Tables)

One of Glide's powerful features is linking the same Big Table to multiple apps. This enables:
- Shared data across related apps
- Central data management
- Consistent user experiences

### How to Link Shared Tables

1. Go to **Data Editor** (Data tab)
2. Find **Data Sources** section in left sidebar
3. Click on your **team name** (e.g., "SpaceX")
4. A **Link Tables** panel appears showing:
   - All Big Tables in your team
   - **Apps Linked** count for each table
   - Last Modified date
   - Searchable table list
5. Check the tables you want to link
6. Click **Link Table** button

### Viewing Linked Tables
The "Apps Linked" column shows how many apps share each table. Click to see which apps use a table.

### Use Cases for Shared Tables

| Pattern | Description |
|---------|-------------|
| **Central Users** | Same Users table across all apps |
| **Shared Products** | Product catalog used by multiple apps |
| **Common Reference** | Departments, Locations shared everywhere |
| **Activity Logs** | Central logging from multiple apps |

### Data Architecture Tips

1. **Plan table sharing upfront** - Decide which data should be centralized
2. **Use Big Tables for shared data** - Native tables can't be shared
3. **Consistent column naming** - Makes relations easier across apps
4. **Single source of truth** - Avoid duplicating data in multiple tables

## API Access for Data Operations

For programmatic data operations, use the Glide API v2:
- Base URL: `https://api.glideapps.com/`
- Works only with Big Tables
- Get API token from: Data Editor > Show API > Copy secret token

See the `api` skill for detailed API usage.

## Best Practices

1. **Name columns clearly** - Use descriptive names that indicate purpose
2. **Create intermediate columns** - Break complex calculations into steps
3. **Use Relations wisely** - They're the foundation of data relationships
4. **Consider user-specific columns** - For per-user data storage
5. **Document your data model** - Use the Description field
6. **Test calculations** - Verify with sample data before going live

## Documentation

- [Data Editor Overview](https://www.glideapps.com/docs/data-editor)
- [Computed Columns](https://www.glideapps.com/docs/computed-columns)
- [Relations](https://www.glideapps.com/docs/reference/computed-columns/relations)
- [Big Tables](https://www.glideapps.com/docs/reference/data-sources/glide-big-tables)
