---
name: data-modeling
description: |
  Design Glide data models - tables, columns, column types, and calculation architecture.
  Use when creating tables, designing data structures, choosing column types, or planning relationships.
---

# Glide Data Modeling

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

## Relation & Lookup Pattern

This is the most common pattern for connecting data:

### Step 1: Create Relation Column
- Creates a link between two tables
- Match rows based on a key column
- Can be single or multiple relations

### Step 2: Create Lookup Column
- References the Relation column
- Pulls specific column values from related rows
- Example: Get manager's phone number via Employee->Manager relation

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
