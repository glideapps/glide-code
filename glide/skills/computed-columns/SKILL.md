---
name: computed-columns
description: |
  Create computed columns in Glide - Math, If-Then-Else, Relations, Rollups, Lookups, Templates.
  Use when adding calculations, formulas, lookups, or linking tables with relations.
---

# Glide Computed Columns

Glide uses **computed columns** instead of cell formulas. Each computed column applies its logic to every row in the table.

## Key Concept

Calculations in Glide are columns, not cell formulas. You build complex logic by chaining columns together - one column's output becomes another column's input.

## Creating Computed Columns

1. In Data Editor, click a column header → "Add column right"
2. Name the column
3. Click the Type dropdown
4. **Use the filter** - type the column type (e.g., "math", "relation", "if-then") to find it quickly
5. Configure the column settings
6. Save

## Math Columns

For arithmetic calculations.

**Important**: Use plain, human-readable column names as variables. No special escaping or symbols needed.

Example - calculating a total:
- Column A: "Quantity"
- Column B: "Unit Price"
- Math column formula: `Quantity * Unit Price`

Just type the column names naturally. Glide will recognize them.

Other examples:
- `Price * 1.1` (add 10% markup)
- `Total / Count` (calculate average)
- `End Date - Start Date` (days between dates)
- `Amount - Discount` (apply discount)

**Configure formatting**: After creating a math column, set up proper display formatting:
- **Decimal places**: 0 for counts, 2 for currency
- **Prefix**: $ or € for money
- **Suffix**: %, kg, mi, hrs for units
- **Thousands separator**: Enable for large numbers

This makes the data instantly readable (e.g., "$1,234.56" instead of "1234.56").

## If-Then-Else Columns

For conditional logic. Returns different values based on conditions.

Structure:
- IF [condition]
- THEN [value if true]
- ELSE [value if false]

Examples:
- Status emoji: IF `Status` is "Complete" THEN "✅" ELSE IF `Status` is "In Progress" THEN "🔄" ELSE "⏳"
- Priority color: IF `Priority` is "High" THEN "🔴" ELSE IF `Priority` is "Medium" THEN "🟡" ELSE "🟢"
- Overdue flag: IF `Days Until Due` < 0 THEN "Overdue" ELSE "On Track"
- Discount tier: IF `Total` > 1000 THEN "Gold" ELSE IF `Total` > 500 THEN "Silver" ELSE "Bronze"

You can chain multiple If-Then-Else for complex logic, or nest them.

## Relation Columns

Connect rows between tables based on matching values. **Relations are the foundation of data relationships in Glide.**

### Best Practice: Use Row IDs

**Always create Row ID columns in both tables before creating relations.**

Why Row IDs are the proper approach:
- **Unique**: Every row gets a unique identifier
- **Stable**: Row IDs never change even if other data updates
- **Reliable**: Prevents relation failures from duplicate names or changing values
- **Standard**: The Glide-recommended pattern

### Creating Row ID Columns

Before creating a relation, ensure both tables have Row ID columns:

1. In Data Editor, open each table
2. Add a column (click column header → "Add column right", or use **CMD+SHIFT+ENTER**)
3. Name it (or leave blank - will auto-name to "Row ID")
4. Change Type to **Basic → Row ID**
5. Glide auto-generates unique IDs for every row

### Creating a Relation

Setup:
1. Create a Relation column in the source table
2. Select the target table
3. Choose which columns to match:
   - **Recommended**: Match a column containing Row IDs to the target table's Row ID column
   - Example: Orders table "Customer" column (contains Row IDs) → Customers table "Row ID" column

Types:
- **Single relation**: Returns one matching row
- **Multiple relation**: Returns all matching rows (for one-to-many)

### Common Pattern: Data Contains Row IDs

Your data often already contains Row ID references:

**Example**: Visits table referencing Clients
- Visits has "Client" column with values like "abc123" (Row IDs, not names!)
- Clients table has "Row ID" column
- Create Relation in Visits: Match "Client" to "Clients → Row ID"
- Add Lookups to pull "Client Name", "Client Phone", etc.

This pattern appears when:
- Forms store user Row IDs when submitting data
- Imported data uses unique IDs for references
- One table links to another via ID field

### Why Not Match on Names?

**Don't create relations by matching name columns:**
- ❌ Names can duplicate (two "John Smith" customers)
- ❌ Names change (user updates their profile)
- ❌ Relations break when names don't match exactly
- ✅ Use Row IDs instead for reliable, stable relations

Use relations to enable Lookups and Rollups.

## Lookup Columns

Pull a value from a related row.

Setup:
1. Create a Lookup column
2. Select a Relation column as the source
3. Choose which column to pull from the related table

Example:
- Orders table has Relation to Customers
- Lookup column pulls "Customer Name" from the related Customer row
- Now each order shows the customer's name

## Rollup Columns

Aggregate values across related rows.

Setup:
1. Create a Rollup column
2. Select a Relation column (usually a multiple relation)
3. Choose the aggregation: Count, Sum, Average, Min, Max
4. Select which column to aggregate (for Sum/Average/Min/Max)

Examples:
- Count of tasks per project
- Sum of order amounts per customer
- Average rating per product
- Max price in a category

**Critical Warning**: Don't use Rollup directly on an entire table when you need per-item aggregation. See "Query Columns" section below for the proper pattern.

## Query Columns

Filter and sort data from other tables. **Query columns are like Relations but with filtering and sorting capabilities.**

### When to Use Query Instead of Basic Rollup

**Problem**: Using Rollup directly on a whole table gives the same result for every row.

**Example of the wrong approach:**
- Clients table needs "Total Visits" per client
- ❌ Creating a Rollup that counts the entire Visits table
- Result: Every client shows the same total (e.g., "5 visits")
- This happens because Rollup counted ALL visits, not per-client visits

**Solution**: Use Query to filter first, then Rollup/Single Value on the Query results.

### Query → Rollup/Single Value Pattern

**The proper pattern for per-item aggregations:**

```
Step 1: Create Query Column (filter the data)
Step 2: Create Rollup/Single Value on the Query (aggregate the filtered results)
```

**Complete Example: Client Visit Statistics**

**Goal**: Each client should show their own visit count and last visit date.

**Setup in Clients Table:**

1. **Query Column: "Client's Visits"**
   - Source: Visits table
   - Filter: Where "Client" matches "Row ID" (this item)
   - Sort: By "Visit Date" descending (newest first)
   - Result: Only THIS client's visits, sorted by date

2. **Rollup Column: "Total Visits"**
   - Source: Client's Visits (the Query column, not the whole table!)
   - Aggregation: Count
   - Result: Count of visits for THIS client only

3. **Single Value Column: "Last Visit Date"**
   - Source: Client's Visits → Visit Date
   - Takes: First item (most recent, because Query is sorted)
   - Result: The most recent visit date for this client

4. **Math Column: "Days Since Last Visit"** (optional)
   - Formula: Now - Last Visit Date
   - Result: Days elapsed since last visit

**Why This Works:**

- ✅ Query filters to only THIS client's visits (Row ID match)
- ✅ Rollup counts the filtered Query results, not the whole table
- ✅ Single Value gets the first item from the sorted Query
- ✅ Each client sees their own statistics

**Common Mistake:**
- ❌ Rollup → Visits table (counts ALL visits for every client)
- ✅ Query → Filter to this client → Rollup (counts per client)

### Query vs Relation

**Similarities:**
- Both connect rows between tables
- Both can be used with Rollup and Single Value

**Differences:**

| Feature | Relation | Query |
|---------|----------|-------|
| **Filtering** | Match on one column only | Multiple filters, conditions |
| **Sorting** | No sorting | Sort by any column |
| **Use Case** | Simple lookups | Filtered aggregations, sorted lists |
| **Example** | Show related customer | Show recent visits for this customer |

**When to use Query:**
- You need to filter data (e.g., only this client's visits)
- You need sorted results (e.g., most recent first)
- You're doing per-item aggregations (count, sum, etc.)

**When to use Relation:**
- Simple one-to-one or one-to-many lookups
- No filtering needed beyond the match column
- Standard Lookup pattern

### Query Configuration

**Source**: Which table to query

**Filter**: Conditions to match rows
- Common pattern: Row ID matches a reference column
- Example: "Client" (in Visits) matches "Row ID" (this Clients row)

**Sort**: Order the results
- Ascending (A→Z, oldest first)
- Descending (Z→A, newest first)
- Example: Sort by "Visit Date" descending for most recent first

**Limit**: Max number of rows to return (optional)

## Single Value Columns

Extract one value from a Query or Relation that returns multiple rows.

Setup:
1. Create a Single Value column
2. Select a Query or Relation column that returns multiple rows
3. Choose which column to extract
4. Select which item: First, Last, Random

**Common Pattern with Query:**
- Query sorted by date descending
- Single Value gets First item
- Result: Most recent value

Example:
- Query: Client's visits sorted by date (newest first)
- Single Value: Visit Date → First
- Shows the most recent visit date

## Template Columns

Combine text and column values into formatted strings.

Example:
- Template: `Hello, {Name}! Your order total is ${Total}.`
- Columns can be inserted with the column picker

Great for:
- Formatted display text
- Email/message templates
- Generating URLs with dynamic values

## Experimental Columns

### Hero Icons
Generate icons from the Hero Icons library.
- Input: Icon name (e.g., "check-circle", "user", "folder")
- Output: Image URL for the icon
- Combine with If-Then-Else for dynamic icons based on status

### Generate Text (AI)
Use AI to generate text based on other columns.
- Configure a prompt that references column values
- AI generates text for each row

### Other AI Columns
- **Image to Text**: Extract text/info from images
- **Text to Choice**: AI categorization
- **Text to Boolean**: AI yes/no classification

## Column Chaining Pattern

Build complex calculations by chaining simple columns:

```
Example: Order Status with Emoji

1. Relation: "Customer" → links to Customers table
2. Lookup: "Customer Name" → pulls name via Customer relation
3. Math: "Days Until Due" → Due Date - Today
4. If-Then-Else: "Is Overdue" → IF Days Until Due < 0 THEN true ELSE false
5. If-Then-Else: "Status Emoji" → IF Is Overdue THEN "🚨" ELSE IF Status = "Complete" THEN "✅" ELSE "📋"
```

Each column does one thing, and they build on each other.

## Tips

- **Name columns clearly** - they become variables in other calculations
- **Use the type filter** - faster than scrolling through menus
- **Chain simple columns** - easier to debug than one complex column
- **Test with sample data** - verify calculations work before adding more
