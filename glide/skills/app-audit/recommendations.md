# Performance Issue Recommendations

Step-by-step guides for fixing performance issues identified by the app audit tool.

## Table of Contents

### Data Layer Fixes
1. [Flattening Computed Column Chains](#1-flattening-computed-column-chains)
2. [Consolidating Relations](#2-consolidating-relations)
3. [Fixing Rollups to Use Relations](#3-fixing-rollups-to-use-relations)
4. [Optimizing AI Column Usage](#4-optimizing-ai-column-usage)
5. [Replacing Query Columns with Relations](#5-replacing-query-columns-with-relations)
6. [Managing Row Limits](#6-managing-row-limits)

### Layout Fixes
7. [Reducing Collection Field Density](#7-reducing-collection-field-density)
8. [Optimizing Inline Collections](#8-optimizing-inline-collections)
9. [Simplifying Component-Heavy Screens](#9-simplifying-component-heavy-screens)
10. [Optimizing Table Views](#10-optimizing-table-views)

---

## Data Layer Fixes

### 1. Flattening Computed Column Chains

**Problem**: Deep chains of dependent computed columns (6+ layers) cause high latency.

**Solution**: Combine logic into fewer columns using If-Then-Else.

#### Step-by-Step Fix

**Before** (6 layers):
```
1. Due Date (Basic)
2. Days Until Due (Math: Due Date - Today)
3. Is Overdue (If-Then-Else: Days Until Due < 0)
4. Status Flag (If-Then-Else: based on Is Overdue + Status)
5. Status Emoji (If-Then-Else: based on Status Flag)
6. Display Status (Template: combines Status Emoji + text)
```

**After** (2-3 layers):
```
1. Due Date (Basic)
2. Days Until Due (Math: Due Date - Today)
3. Status Display (If-Then-Else: combines ALL logic)
   IF Days Until Due < 0 THEN "🚨 Overdue"
   ELSE IF Status = "Complete" THEN "✅ Complete"
   ELSE IF Days Until Due <= 1 THEN "⚠️ Due Soon"
   ELSE "📋 Active"
```

**Steps to Implement**:

1. **Identify the chain** - List all columns in dependency order
2. **Map the logic** - Write out what each column calculates
3. **Create combined column** - New If-Then-Else with all conditions
4. **Test thoroughly** - Verify logic matches old columns
5. **Update UI references** - Point components to new column
6. **Delete old columns** - Remove intermediate columns

**Performance Gain**: 60-80% reduction in computation time

---

### 2. Consolidating Relations

**Problem**: Too many relation columns (5+) slow down data loading.

**Solution**: Evaluate necessity, consolidate where possible, use sub-relations.

#### Step-by-Step Fix

**Before** (8 relations):
```
Customers table:
- Related Orders
- Related Invoices
- Related Payments
- Related Support Tickets
- Related Activities
- Related Contacts
- Related Documents
- Related Notes
```

**After** (3-4 essential relations):
```
Customers table:
- Related Orders (keep - frequently used)
- Related Contacts (keep - essential)
- Related Support Tickets (keep - for support team)

Access others via sub-relations:
- Orders → Invoices
- Orders → Payments
```

**Steps to Implement**:

1. **Audit relation usage** - Which are actually used in UI?
2. **Prioritize essential relations**:
   - Direct business logic
   - Frequently displayed
   - Required for workflows
3. **Identify removable relations**:
   - Rarely used
   - Accessible via other paths
   - Nice-to-have but not critical
4. **Remove unused relations** - Delete low-priority relation columns
5. **Update navigation** - Use "View Related" buttons for occasional access
6. **Test workflows** - Ensure critical paths still work

**Performance Gain**: 30-50% reduction in screen load time

---

### 3. Fixing Rollups to Use Relations

**Problem**: Rollups operating on entire tables instead of filtered relations.

**Solution**: Always create a relation first, then rollup on that relation.

#### Step-by-Step Fix

**Before** (WRONG):
```
Projects table:
- Task Count (Rollup)
  - Source: Tasks table (entire table!)
  - Function: Count
  - Result: Every project shows total of ALL tasks
```

**After** (CORRECT):
```
Projects table:
1. Related Tasks (Relation)
   - Match: Projects.Row ID → Tasks.projectId

2. Task Count (Rollup)
   - Source: Related Tasks relation
   - Function: Count
   - Result: Each project shows only its own tasks
```

**Steps to Implement**:

1. **Add Row ID if missing**:
   - Projects table: Add Row ID column
   - Tasks table: Add Row ID column

2. **Add foreign key column**:
   - Tasks table: Add "projectId" (Text column)
   - Populate with Project Row ID values

3. **Create Relation**:
   - In Projects table: Add Relation column "Related Tasks"
   - Match: Projects.Row ID → Tasks.projectId
   - Type: Multiple (one project → many tasks)

4. **Update Rollup**:
   - Edit existing Rollup column
   - Change source from "Tasks table" to "Related Tasks relation"
   - Save

5. **Verify results**:
   - Check each project shows correct count
   - Test with different projects

**Performance Gain**: Eliminates wasteful processing, provides correct data

---

### 4. Optimizing AI Column Usage

**Problem**: Multiple AI columns (3+) per table cause slow performance.

**Solution**: Consolidate AI operations, use comprehensive prompts, limit AI columns.

#### Step-by-Step Fix

**Before** (5 AI columns):
```
Products table:
1. Generate Description (AI)
2. Generate SEO Title (AI)
3. Generate Keywords (AI)
4. Extract Image Info (AI)
5. Categorize Product (AI)
```

**After** (2 AI columns):
```
Products table:
1. Generate All Text (AI Generate Text)
   Prompt: "Based on {Product Name}, generate:
   - Product description (2-3 sentences)
   - SEO-optimized title
   - 5 relevant keywords
   Format as: Description: ... | Title: ... | Keywords: ..."

2. Extract Image Info (AI Image to Text) ← Keep if needed
   Only when images present

Parse results:
3. Description (Template: extract from Generate All Text)
4. SEO Title (Template: extract from Generate All Text)
5. Keywords (Template: extract from Generate All Text)
```

**Steps to Implement**:

1. **Audit AI column usage**:
   - Which AI columns are essential?
   - Can any be combined?
   - Are results actually used?

2. **Create comprehensive prompt**:
   - Combine multiple AI tasks into one prompt
   - Use structured output format
   - Reference multiple input columns

3. **Add parsing columns**:
   - Use Template or Split Text columns
   - Extract specific parts from AI output
   - No additional AI cost!

4. **Test output quality**:
   - Verify AI generates all required info
   - Adjust prompt for better results

5. **Remove old AI columns**:
   - Update UI references
   - Delete redundant AI columns

**Performance Gain**: 50-70% reduction in AI processing time

**Cost Savings**: Significant AI credit reduction

---

### 5. Replacing Query Columns with Relations

**Problem**: Multiple Query columns causing repeated database queries.

**Solution**: Use one Relation + multiple Lookups.

#### Step-by-Step Fix

**Before** (3 Query columns):
```
Orders table:
1. Customer Name (Query: SELECT name FROM customers ...)
2. Customer Email (Query: SELECT email FROM customers ...)
3. Customer Phone (Query: SELECT phone FROM customers ...)
```

**After** (1 Relation + 3 Lookups):
```
Orders table:
1. Customer (Relation)
   - Match: Orders.customerId → Customers.Row ID

2. Customer Name (Lookup)
   - Via: Customer relation
   - Column: name

3. Customer Email (Lookup)
   - Via: Customer relation
   - Column: email

4. Customer Phone (Lookup)
   - Via: Customer relation
   - Column: phone
```

**Steps to Implement**:

1. **Create Relation**:
   - Identify foreign key column (e.g., customerId)
   - Create Relation matching to Customers.Row ID

2. **Create Lookups**:
   - For each Query column, create Lookup
   - Set source to new Relation
   - Select column to retrieve

3. **Test data**:
   - Verify Lookups return same data as Queries
   - Check with multiple rows

4. **Update UI**:
   - Replace Query column references with Lookups
   - Test all screens

5. **Delete Query columns**:
   - Once verified, remove old Query columns

**Performance Gain**: 60-80% faster data retrieval

---

### 6. Managing Row Limits

**Problem**: Tables approaching or exceeding row limits.

**Solutions**: Archive data, implement retention, upgrade to Big Tables.

#### Strategy A: Data Archiving

**For tables with historical data that's rarely accessed:**

1. **Create Archive table**:
   - Same schema as original table
   - Named "{TableName} Archive"

2. **Set archival criteria**:
   - Records older than 12 months
   - Completed status + 90 days old
   - Records marked "archived"

3. **Create archival workflow** (scheduled):
   - Query: Find records meeting criteria
   - Loop through matches
   - Add row to Archive table
   - Delete row from original table

4. **Access archived data**:
   - Create "View Archive" screen
   - Users can search/browse archived records

**Result**: Keeps active table under 50% capacity

#### Strategy B: Data Retention Policy

**For tables with truly temporary data:**

1. **Add deletion criteria column**:
   - "Delete After" (Date)
   - Or use: Created Date + 90 days

2. **Create cleanup workflow** (scheduled daily):
   - Query: Find records past deletion date
   - Loop through matches
   - Delete row

3. **Set retention periods**:
   - Logs: 30 days
   - Activities: 90 days
   - Temporary data: 7 days

**Result**: Automatic cleanup, no manual intervention

#### Strategy C: Upgrade to Big Tables

**For tables needing high capacity:**

1. **Export data** from current table
2. **Create Big Table** via API or UI
3. **Import data** to Big Table
4. **Link Big Table** to app
5. **Update UI** to use Big Table
6. **Delete old table**

**Big Table Limits**:
- Free: 25,000 rows
- Pro: 100,000 rows
- Business: 1,000,000+ rows

**Performance Gain**: Room to grow + better performance

---

## Layout Fixes

### 7. Reducing Collection Field Density

**Problem**: Card collections showing too many fields (10+).

**Solution**: Display only 3-5 key fields, move details to detail screen.

#### Step-by-Step Fix

**Before** (12 fields):
```
Tasks Collection (Card style):
- Title
- Full Description (long text)
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

**After** (4 fields):
```
Tasks Collection (Card style):
- Title (prominent)
- Status emoji + Due Date (combined)
- Assignee (avatar)
- Priority badge

Detail screen shows ALL fields
```

**Steps to Implement**:

1. **Navigate to collection screen** in Layout Editor

2. **Configure collection component**:
   - Click collection component
   - Open "Fields" settings
   - Uncheck fields to hide (keep 3-5 essential)

3. **Choose essential fields**:
   - Title/Name (always show)
   - Status indicator (emoji/badge)
   - Key date (due date, deadline)
   - Owner/assignee (avatar)

4. **Combine related fields**:
   - Create Template column: "{Status Emoji} Due {Due Date}"
   - Reduces 2 fields to 1

5. **Save and test**:
   - Preview collection
   - Verify it's scannable
   - Check mobile view

6. **Design detail screen**:
   - Click item to navigate to detail
   - Add all remaining fields here
   - Use Title, Fields, Containers for organization

**Performance Gain**: 50-70% faster collection rendering

**UX Gain**: More scannable, less overwhelming

---

### 8. Optimizing Inline Collections

**Problem**: Detail screens with 3+ inline collections cause slow loading.

**Solution**: Limit to 1-2 inline collections, use navigation for others.

#### Step-by-Step Fix

**Before** (5 inline collections):
```
Customer Detail Screen:
- Related Orders (inline, 20 rows)
- Related Invoices (inline, 15 rows)
- Related Support Tickets (inline, 30 rows)
- Related Activities (inline, 50 rows)
- Related Documents (inline, 25 rows)

Total: 140 rows rendered
```

**After** (1-2 inline collections + buttons):
```
Customer Detail Screen:
- Recent Orders (inline, filtered to last 10)

Navigation Buttons:
- View All Orders → separate screen
- View Invoices → separate screen
- View Support Tickets → separate screen
- View Activities → separate screen
- View Documents → separate screen
```

**Steps to Implement**:

1. **Identify most important collection**:
   - What do users view most often?
   - Keep this as inline (limit 10-20 rows)

2. **Delete other inline collections**:
   - Select inline collection components
   - Delete them

3. **Add navigation buttons**:
   - Add Button components
   - Configure "Show New Screen" action
   - Create target screen for each

4. **Create dedicated screens**:
   - Add Custom Screen
   - Add Collection component
   - Bind to relevant relation
   - Add filters, sorting as needed

5. **Add "Back" navigation**:
   - Ensure users can return to detail screen

**Performance Gain**: 60-80% faster screen load

**UX Gain**: Focused, progressive disclosure

---

### 9. Simplifying Component-Heavy Screens

**Problem**: Screens with 20+ components are slow and cluttered.

**Solution**: Reduce to 10-15 components, use Tabs Container for organization.

#### Step-by-Step Fix

**Before** (35 components):
```
Dashboard Screen:
- Cover title
- 6 Big Numbers
- 4 Progress bars
- 3 Charts
- 2 Maps
- 5 Inline collections
- 8 Buttons
- 6 Separators
```

**After** (12 components with tabs):
```
Dashboard Screen:
- Cover title
- Tabs Container:

  Tab 1: Overview (8 components)
  - 3 Key Big Numbers
  - 1 Summary chart
  - 2 Buttons
  - 2 Separators

  Tab 2: Metrics (10 components)
  - 4 Progress bars
  - 2 Charts
  - 3 Buttons
  - 1 Separator

  Tab 3: Details (8 components)
  - 2 Maps
  - 2 Inline collections
  - 3 Buttons
  - 1 Separator
```

**Steps to Implement**:

1. **Audit current components**:
   - List all components
   - Group by purpose/theme

2. **Determine tab structure**:
   - Overview/Summary tab
   - Detailed/Metrics tab
   - Actions/Settings tab

3. **Add Tabs Container** (if available):
   - In Layout Editor, add Tabs Container component
   - Or create multiple Custom Screens linked by buttons

4. **Move components to tabs**:
   - Organize by theme
   - Keep most important in first tab

5. **Remove redundant components**:
   - Delete duplicate information
   - Combine similar components
   - Remove "nice to have" items

6. **Test navigation**:
   - Verify tabs work
   - Check mobile behavior

**Performance Gain**: 40-60% faster screen load

**UX Gain**: Less overwhelming, better organization

---

### 10. Optimizing Table Views

**Problem**: Table collections showing expensive computed columns (especially AI).

**Solution**: Hide expensive columns from collection view, show on detail screens only.

#### Step-by-Step Fix

**Before**:
```
Orders table collection showing:
- Order ID
- Customer Name (Lookup)
- Total (Math)
- Tax (Math)
- Grand Total (Math)
- Status Summary (AI Generate Text) ← 2-3 seconds each!
- Related Items Count (Rollup)

50 rows visible → 100-150 seconds of AI processing!
```

**After**:
```
Orders table collection showing:
- Order ID
- Customer Name (Lookup)
- Grand Total (Math) ← Fast
- Status emoji (If-Then-Else) ← Fast

Hidden in collection (show on detail screen):
- Individual calculations
- AI-generated summaries
- Rollup counts
```

**Steps to Implement**:

1. **Navigate to table collection** in Layout Editor

2. **Identify expensive columns**:
   - AI columns (Generate Text, Image to Text, etc.)
   - Rollups
   - Heavy math calculations

3. **Hide from collection**:
   - Click table collection component
   - Open "Columns" or "Fields" settings
   - Uncheck expensive columns

4. **Keep essential columns**:
   - ID/primary field
   - Key identifiers (name, title)
   - Simple calculated fields (math)
   - Status indicators (emoji)

5. **Add to detail screen**:
   - Navigate to detail screen
   - Add Fields component
   - Show all columns including expensive ones
   - Only computed once per viewed item

6. **Test performance**:
   - Load collection - should be fast
   - Click item - detail loads with full data

**Performance Gain**: 80-95% faster collection loading

---

## Best Practices for Preventing Issues

### Data Modeling

1. **Add Row ID columns first** - Before creating any relations
2. **Limit computed column depth** - Max 3 layers
3. **Use relations for cross-table data** - Avoid Query columns
4. **Rollups need relations** - Never rollup entire tables
5. **Limit AI columns** - Use sparingly, combine prompts

### Layout Design

6. **Collections show summaries** - Details go on detail screens
7. **Limit inline collections** - Max 1-2 per screen
8. **Use progressive disclosure** - Don't show everything at once
9. **Mobile-first thinking** - Less is more on small screens
10. **Test with real data** - Use realistic row counts

### Performance Monitoring

11. **Regular audits** - Monthly for growing apps
12. **Monitor row counts** - Set alerts at 70% capacity
13. **Test load times** - Benchmark key screens
14. **User feedback** - Ask about slowness
15. **Profile before scaling** - Audit before adding major data

---

## Quick Reference

### Fix Priority Matrix

| Issue | Severity | Time to Fix | Impact | Priority |
|-------|----------|-------------|--------|----------|
| Rollup without relation | Critical | 10 min | High | **Do First** |
| 6+ layer column chain | Critical | 30 min | High | **Do First** |
| Row limit exceeded | Critical | 1-2 hours | High | **Do First** |
| 5+ AI columns | Warning | 45 min | High | **Do Soon** |
| 5+ relations | Warning | 30 min | Medium | **Do Soon** |
| 3+ inline collections | Warning | 20 min | Medium | **Do Soon** |
| 10+ fields in card | Optimization | 15 min | Low | **When Possible** |
| 20+ components | Warning | 1 hour | Medium | **Do Soon** |

---

## Getting Help

If you need assistance implementing these fixes:

1. **Review the audit report** - Specific locations and recommendations
2. **Check Glide docs** - [glideapps.com/docs](https://glideapps.com/docs)
3. **Ask in Glide Community** - [community.glideapps.com](https://community.glideapps.com)
4. **Use Claude Code** - Can help implement fixes via browser automation

---

## References

- [Glide Computed Columns](../computed-columns/SKILL.md)
- [Glide Data Modeling](../data-modeling/SKILL.md)
- [Glide Design](../design/SKILL.md)
- [Glide Performance Best Practices](https://www.glideapps.com/docs/performance)
