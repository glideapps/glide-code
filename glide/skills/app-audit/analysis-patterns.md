# Performance Anti-Patterns in Glide Apps

Detailed documentation of performance issues detected by the app audit tool, with examples and impact analysis.

## Table of Contents

### Data Layer Anti-Patterns
1. [Deep Computed Column Chains](#1-deep-computed-column-chains)
2. [Relation Proliferation](#2-relation-proliferation)
3. [Rollups Without Relations](#3-rollups-without-relations)
4. [Heavy AI Column Usage](#4-heavy-ai-column-usage)
5. [Query Column Overuse](#5-query-column-overuse)
6. [Row Limit Issues](#6-row-limit-issues)

### Layout Anti-Patterns
7. [Collection Field Density](#7-collection-field-density)
8. [Inline Collection Overload](#8-inline-collection-overload)
9. [Component Overload](#9-component-overload)
10. [Unoptimized Table Views](#10-unoptimized-table-views)

---

## Data Layer Anti-Patterns

### 1. Deep Computed Column Chains

**What It Is**: Creating long chains of computed columns where each column depends on the previous one.

**Detection Criteria**:
- **Critical (🔴)**: 6+ layers deep
- **Warning (🟡)**: 4-5 layers deep

**Example - BAD ❌**:
```
Tasks table:
1. Due Date (Basic column)
2. Days Until Due (Math: Due Date - Today)
3. Is Overdue (If-Then-Else: Days Until Due < 0)
4. Status Flag (If-Then-Else: based on Is Overdue)
5. Status Emoji (If-Then-Else: based on Status Flag)
6. Display Status (Template: combines Status Emoji + Status Flag)
```

**Why It's Bad**:
- Each layer adds computation time
- Changes propagate through entire chain
- Harder to debug issues
- Increased latency on every row operation

**Performance Impact**:
- 6-layer chain: ~300-500ms latency per row
- Affects: Loading screens, adding data, editing records
- Multiplied by number of rows displayed

**Example - GOOD ✅**:
```
Tasks table:
1. Due Date (Basic column)
2. Days Until Due (Math: Due Date - Today)
3. Status Display (If-Then-Else: combines all logic in one column)
   - IF Days Until Due < 0 THEN "🚨 Overdue"
   - ELSE IF Status = "Complete" THEN "✅ Done"
   - ELSE "📋 Active"
```

**Detection Method**:
1. Build column dependency graph
2. Count depth from leaf nodes to basic columns
3. Flag chains exceeding thresholds

---

### 2. Relation Proliferation

**What It Is**: Creating too many relation columns in a single table.

**Detection Criteria**:
- **Warning (🟡)**: 5+ relation columns
- **Critical (🔴)**: 8+ relation columns

**Example - BAD ❌**:
```
Customers table with 8 relations:
- Related Orders
- Related Invoices
- Related Payments
- Related Support Tickets
- Related Activities
- Related Contacts
- Related Documents
- Related Notes
```

**Why It's Bad**:
- Each relation triggers database lookups
- All relations compute even if not displayed
- Memory overhead loading related data
- Slower screen rendering

**Performance Impact**:
- Each relation: ~50-150ms lookup time
- 8 relations: ~400-1200ms just for relations
- Compounds when using lookups/rollups

**Example - GOOD ✅**:
```
Customers table with 3 essential relations:
- Related Orders (most important)
- Related Contacts (frequently used)
- Related Support Tickets (for support team)

Other data accessible via:
- Orders → Invoices (sub-relation)
- Orders → Payments (sub-relation)
```

**Detection Method**:
1. Count relation-type columns per table
2. Flag tables exceeding thresholds
3. List all relations for review

---

### 3. Rollups Without Relations

**What It Is**: Creating rollup columns that operate on entire tables instead of filtered relations.

**Detection Criteria**:
- **Critical (🔴)**: Rollup directly on table (not relation)

**Example - BAD ❌**:
```
Projects table:
- Task Count (Rollup)
  - Source: Tasks table (entire table!)
  - Function: Count rows

Result: Every project shows total count of ALL tasks in the app
```

**Why It's Bad**:
- Processes every row in target table
- Returns same value for all rows (useless)
- Wastes computation on irrelevant data
- Logical error - not filtered to project

**Performance Impact**:
- Processes 1000s of rows unnecessarily
- No benefit (returns wrong data)
- Scales poorly with data growth

**Example - GOOD ✅**:
```
Projects table:
1. Related Tasks (Relation)
   - Match: Project ID → Tasks.Project ID

2. Task Count (Rollup)
   - Source: Related Tasks relation
   - Function: Count rows

Result: Each project shows only its own task count
```

**Detection Method**:
1. Identify rollup columns
2. Check if source is relation or table
3. Flag rollups sourcing tables directly

---

### 4. Heavy AI Column Usage

**What It Is**: Using too many AI-powered columns (Generate Text, Image to Text, etc.) which are computationally expensive.

**Detection Criteria**:
- **Warning (🟡)**: 3+ AI columns per table
- **Critical (🔴)**: 5+ AI columns per table

**Example - BAD ❌**:
```
Products table:
1. Generate Description (AI Generate Text)
2. Generate SEO Title (AI Generate Text)
3. Generate Keywords (AI Generate Text)
4. Extract Image Info (AI Image to Text)
5. Categorize Product (AI Text to Choice)
6. Generate Tags (AI Text to Texts)
```

**Why It's Bad**:
- AI inference is slow (1-3 seconds per column)
- Consumes AI credits rapidly
- 6 AI columns: 6-18 seconds per row!
- Blocks other operations

**Performance Impact**:
- Generate Text: ~2-3 seconds per row
- Image to Text: ~1-2 seconds per row
- Text to Choice: ~1 second per row
- Multiple AI columns: Cumulative delay

**Example - GOOD ✅**:
```
Products table:
1. Generate Description (AI Generate Text) - One comprehensive prompt
2. Extract Image Info (AI Image to Text) - When images present

Combined approach:
- Single Generate Text with comprehensive prompt that includes description, SEO, and keywords
- Reduces 3 AI calls to 1
```

**Detection Method**:
1. Identify AI column types
2. Count per table
3. Calculate total AI cost/latency

---

### 5. Query Column Overuse

**What It Is**: Using multiple Query columns for cross-table lookups instead of relations + lookups.

**Detection Criteria**:
- **Warning (🟡)**: 2+ Query columns per table

**Example - BAD ❌**:
```
Orders table:
1. Customer Name (Query: SELECT name FROM customers WHERE id = customerId)
2. Customer Email (Query: SELECT email FROM customers WHERE id = customerId)
3. Customer Phone (Query: SELECT phone FROM customers WHERE id = customerId)
```

**Why It's Bad**:
- Each Query column = separate database query
- 3 queries when 1 relation could handle all
- Harder to maintain
- Performance scales poorly

**Performance Impact**:
- Each Query: ~100-300ms
- 3 Queries: ~300-900ms
- Repeated for every row displayed

**Example - GOOD ✅**:
```
Orders table:
1. Customer (Relation: match customerId → Customers.Row ID)
2. Customer Name (Lookup: via Customer relation → name)
3. Customer Email (Lookup: via Customer relation → email)
4. Customer Phone (Lookup: via Customer relation → phone)

Result: 1 relation lookup, 3 instant value retrievals
```

**Detection Method**:
1. Count Query-type columns per table
2. Flag tables with 2+
3. Suggest relation alternatives

---

### 6. Row Limit Issues

**What It Is**: Tables approaching or exceeding Glide row limits.

**Detection Criteria**:
- **Warning (🟡)**: 80% of row limit (20k for free, 200k for paid)
- **Critical (🔴)**: 95%+ of row limit

**Example - ISSUE**:
```
Activities table:
- Current rows: 18,500
- Plan limit: 25,000 rows
- Utilization: 74% (approaching warning)

Growth rate: 500 rows/week
→ Will hit limit in 13 weeks
```

**Why It's Bad**:
- New data stops syncing
- Data loss risk
- App errors when limit exceeded
- Performance degrades near limit

**Performance Impact**:
- 15k+ rows: Noticeable slowdown
- 20k+ rows: Significant delays
- Near limit: Severe performance issues
- Over limit: App failures

**Solutions**:
1. **Archive old data** - Move to separate archive table
2. **Implement data retention** - Auto-delete after X months
3. **Use Big Tables** - Higher limits (1M+ rows)
4. **Implement pagination** - Don't load all rows at once
5. **Data cleanup workflows** - Scheduled deletion of obsolete records

**Detection Method**:
1. Get row count per table via API
2. Compare to plan limits
3. Calculate utilization percentage
4. Flag warnings and criticals

---

## Layout Anti-Patterns

### 7. Collection Field Density

**What It Is**: Displaying too many fields in collection views (card, list, table styles).

**Detection Criteria**:
- **Optimization (🟢)**: Card showing 10+ fields
- **Warning (🟡)**: Card showing 15+ fields

**Example - BAD ❌**:
```
Tasks collection (Card style):
Showing 12 fields:
- Title
- Description (full text)
- Status
- Priority
- Assignee
- Due Date
- Created Date
- Tags
- Category
- Project
- Estimated Hours
- Completed %
```

**Why It's Bad**:
- Information overload
- Slow rendering (more DOM elements)
- Hard to scan
- Poor mobile experience

**Performance Impact**:
- 12 fields/card: ~150-250ms render time
- 50 cards: 7.5-12.5 seconds total
- Slower scrolling
- Memory usage increases

**Example - GOOD ✅**:
```
Tasks collection (Card style):
Showing 4 fields:
- Title
- Status emoji + Due Date
- Assignee avatar
- Priority badge

Detail screen shows full info
```

**Detection Method**:
1. Navigate to each collection screen
2. Count visible fields in card/list
3. Flag collections >10 fields

---

### 8. Inline Collection Overload

**What It Is**: Displaying too many inline collections on a single detail screen.

**Detection Criteria**:
- **Warning (🟡)**: 3+ inline collections on one screen

**Example - BAD ❌**:
```
Customer Detail Screen with 5 inline collections:
1. Related Orders (showing 20 orders)
2. Related Invoices (showing 15 invoices)
3. Related Support Tickets (showing 30 tickets)
4. Related Activities (showing 50 activities)
5. Related Documents (showing 25 documents)

Total rows rendered: 140
```

**Why It's Bad**:
- Each collection = separate query
- 5 queries on page load
- 140 rows to render
- Very slow screen load

**Performance Impact**:
- Each inline collection: ~200-500ms
- 5 collections: 1-2.5 seconds
- Plus rendering 140 rows: +2-4 seconds
- Total: 3-6.5 seconds to load screen

**Example - GOOD ✅**:
```
Customer Detail Screen with 1-2 inline collections:
1. Recent Orders (showing 10 most recent)
2. Open Support Tickets (filtered to active only)

+ Action buttons to:
- View All Orders → separate screen
- View All Tickets → separate screen
- View Documents → separate screen
```

**Detection Method**:
1. Navigate to detail screens
2. Count inline collection components
3. Flag screens with 3+

---

### 9. Component Overload

**What It Is**: Using too many components on a single screen.

**Detection Criteria**:
- **Warning (🟡)**: 20+ components per screen
- **Critical (🔴)**: 30+ components per screen

**Example - BAD ❌**:
```
Dashboard Screen with 35 components:
- 1 Cover title
- 6 Big Numbers
- 4 Progress bars
- 3 Charts
- 2 Maps
- 5 Inline collections
- 8 Buttons
- 6 Separators

Result: Cluttered, slow, overwhelming
```

**Why It's Bad**:
- Heavy rendering load
- Slow screen transitions
- Poor user experience
- Hard to find information

**Performance Impact**:
- 35 components: ~1-2 seconds render
- Complex components (charts, maps): +500ms each
- Total: 3-5 seconds screen load

**Example - GOOD ✅**:
```
Dashboard Screen with 12 components:
- 1 Cover title
- 3 Key Big Numbers
- 1 Primary chart
- 1 Summary collection (top items)
- 3 Action buttons
- 3 Separators

Use Tabs Container to organize:
- Tab 1: Overview (shown above)
- Tab 2: Detailed Metrics
- Tab 3: Reports
```

**Detection Method**:
1. Navigate to each screen
2. Count total components
3. Flag screens exceeding thresholds

---

### 10. Unoptimized Table Views

**What It Is**: Displaying computed columns (especially expensive ones) in table/collection views unnecessarily.

**Detection Criteria**:
- **Optimization (🟢)**: Table showing 3+ computed columns
- **Warning (🟡)**: Table showing AI columns or rollups

**Example - BAD ❌**:
```
Orders table collection showing:
- Order ID
- Customer Name (Lookup)
- Total (Math)
- Tax (Math)
- Grand Total (Math)
- Status Summary (AI Generate Text) ← Expensive!
- Related Items Count (Rollup)

Table shows 50 rows → 50 AI generations + 50 rollups
```

**Why It's Bad**:
- Computed columns calculate for ALL visible rows
- AI columns especially slow (2-3 sec each)
- 50 rows * 2 sec = 100 seconds!
- Rollups query related data per row

**Performance Impact**:
- Math columns: Low impact
- Lookups: Low impact
- Rollups: Medium impact (~100ms each)
- AI columns: HIGH impact (2-3 sec each)

**Example - GOOD ✅**:
```
Orders table collection showing:
- Order ID
- Customer Name (Lookup)
- Grand Total (Math) ← Needed for scanning
- Status emoji (If-Then-Else) ← Fast

Hide from collection:
- Individual calculations
- AI-generated summaries (show on detail screen only)
- Rollup counts (show on detail screen)
```

**Detection Method**:
1. Navigate to table collections
2. Identify visible columns
3. Check column types (computed, AI, rollup)
4. Flag tables showing expensive columns

---

## Detection Algorithms

### Column Dependency Graph

Build a directed graph of column dependencies:

```
Algorithm:
1. For each table:
   a. Create node for each column
   b. For computed columns, identify input columns
   c. Create edges: input → computed
2. Find leaf nodes (basic columns)
3. Calculate depth from each leaf to root
4. Flag chains exceeding thresholds
```

### Rollup Validation

Check if rollups use relations:

```
Algorithm:
1. Find all Rollup columns
2. For each rollup:
   a. Check source type
   b. If source = table → FLAG (bad)
   c. If source = relation → OK (good)
3. Report flagged rollups
```

### Collection Field Counter

Count visible fields in collections:

```
Algorithm:
1. Navigate to collection screen
2. Take snapshot of UI
3. Identify collection component
4. Count visible field bindings
5. Flag if count > threshold
```

---

## Severity Scoring

### Critical (🔴) - Fix Immediately
- 6+ layer computed column chains
- Rollups on tables (not relations)
- Circular dependencies
- 95%+ of row limit
- 5+ AI columns per table
- 30+ components per screen

### Warning (🟡) - Address Soon
- 4-5 layer computed column chains
- 5+ relations per table
- 3+ AI columns per table
- 2+ Query columns per table
- 80-94% of row limit
- 3+ inline collections per screen
- 20+ components per screen

### Optimization (🟢) - Improve When Possible
- Moderate column chains (3-4 layers)
- 10+ fields in card collections
- Showing computed columns in tables
- Missing Row ID columns
- Inconsistent naming patterns

---

## References

- [Glide Computed Columns](../computed-columns/SKILL.md)
- [Glide Data Modeling](../data-modeling/SKILL.md)
- [Glide Design Patterns](../design/SKILL.md)
- [Glide API Documentation](https://apidocs.glideapps.com)
