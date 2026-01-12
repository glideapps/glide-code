# Performance Analysis Patterns

This document describes the performance anti-patterns to look for during a Glide app audit, based on official Glide documentation and community guidance.

## Official Glide Thresholds

These are the only documented thresholds from Glide:

| Metric | Threshold | Source |
|--------|-----------|--------|
| Column computation time | 100ms | [Performance Analysis docs](https://www.glideapps.com/docs/performance-analysis) |
| Query column entries | 10,000 | [Query Column Limits](https://help.glideapps.com/en/articles/12063345-understanding-query-column-limits-in-glide-apps) |
| Glide Table rows | 25,000 | [Big Tables Guide](https://www.glideapps.com/docs/guide-to-bigtables) |
| Big Table rows | 10,000,000 | [Big Tables](https://www.glideapps.com/docs/big-tables) |
| Big Table rollup/lookup matches | 100 | [Big Tables](https://www.glideapps.com/docs/big-tables) |
| Collection items visible | 24 | [Build for Speed](https://www.glideapps.com/blog/build-speed-and-scale) |
| Image dimensions | 1,500px | [Build for Speed](https://www.glideapps.com/blog/build-speed-and-scale) |

---

## Critical Issues

### Query on Same Table

**What it is**: A Query column that queries its own table.

**Source**: [Build for Speed and Scale](https://www.glideapps.com/blog/build-speed-and-scale)
> "The worst thing you can do is make a query on its own table. This can just crash your app"

**Detection**: In Data Editor, click on Query columns and check if the source table matches the table the column is in.

**Fix**: Restructure data to avoid self-referential queries. Consider:
- Using Relations instead
- Moving data to a separate lookup table
- Using If-Then-Else for conditional logic

---

### Tables Exceeding Row Limits

**What it is**: Glide Tables with more than 25,000 rows.

**Source**: [Big Tables Guide](https://www.glideapps.com/docs/guide-to-bigtables)

**Detection**:
- Via API: `GET /tables` returns row counts
- Via Data Editor: Row count shown in table header

**Fix**: Migrate to Big Tables. Note limitations:
- Rollups/Lookups limited to 100 matching rows
- Cannot be used as User Profile table
- Some computed column types unavailable

---

### Columns Exceeding 100ms

**What it is**: Computed columns that take more than 100ms to calculate.

**Source**: [Performance Analysis](https://www.glideapps.com/docs/performance-analysis)
> "⚠️ indicates that the column or table is computing results slowly and is taking longer than 100ms"

**Detection**:
1. Open Data Editor
2. Click Performance Analysis button
3. Columns showing ⚠️ exceed 100ms

**Common causes** (from same doc):
- Dependent computed columns (waiting for other computed columns)
- External data source delays (webhooks, third-party platforms)
- Query columns

**Fix**: Depends on cause:
- For dependencies: Reduce chain depth, combine columns
- For queries: Convert to Relations if possible
- For external sources: Consider caching or async patterns

---

## Performance Anti-Patterns

### Query Columns vs Relations

**What it is**: Using Query columns when Relations would work.

**Source**: [Query Column docs](https://www.glideapps.com/docs/query-column)
> "Query columns are often the source of performance issues. Consider replacing query columns with Conditional Relations to improve performance speed."

**Detection**:
- Find all Query columns in app
- Check if they're doing simple value matching
- If yes, a Relation would be faster

**When Query is necessary**:
- Complex filtering logic Relations can't handle
- When source is Big Table or SQL (no difference then)

**Fix**:
1. Create Relation column with same matching logic
2. Update any Lookups/Rollups to use the Relation
3. Delete the Query column

---

### Nested Computed Columns

**What it is**: Computed columns that depend on other computed columns in chains.

**Source**: [Performance Tips](https://community.glideapps.com/t/app-performance-tips-tricks-speed/73139)
> "Nested computed columns (relying on other computed columns) cause substantial slowdowns with large datasets"

**Source**: [Performance Analysis](https://www.glideapps.com/docs/performance-analysis)
> "Computed columns with dependencies on other computed columns are likely to slow down as they wait for the other columns in the chain to process."

**Detection**:
- Use Dev Tools plugin (Column Dependencies tab) if available
- Otherwise, trace manually: click each computed column, see what it references

**Impact**: Each level adds latency. The exact impact depends on:
- Data volume
- Complexity of each computation
- Whether columns are used in filtering

**Fix**:
- Combine multiple steps into single JavaScript column
- Use If-Then-Else with multiple conditions instead of chained columns
- Pre-calculate values via workflows for frequently-accessed data

---

### Filtering/Sorting on Computed Columns

**What it is**: Using computed column values for collection filters or sorts.

**Source**: [Performance Tips](https://community.glideapps.com/t/app-performance-tips-tricks-speed/73139)
> "Avoid filtering or sorting on computed columns—this forces entire table recalculation"

**Detection**: In Layout Editor, check collection filter/sort configurations for computed column sources.

**Fix**:
- Filter on basic columns when possible
- Pre-calculate filter values into basic columns via workflows
- Use Row Owners for user-specific filtering (computed at write time)

---

### Large Collections Without Pagination

**What it is**: Collections showing more than 24 items at once.

**Source**: [Build for Speed and Scale](https://www.glideapps.com/blog/build-speed-and-scale)
> "Include a maximum of 24 items viewable at a time"

**Detection**: In Layout Editor, check collection configuration for item limits.

**Fix**:
- Enable pagination
- Add search/filter to reduce displayed items
- Use "Load more" pattern

---

### Large Images

**What it is**: Images larger than 1,500px in any dimension.

**Source**: [Build for Speed and Scale](https://www.glideapps.com/blog/build-speed-and-scale)
> "No need for images more than 1,500 px squared in a Glide app"

**Detection**: Check image dimensions in source files or via browser dev tools.

**Fix**:
- Compress images before upload
- Glide uses Cloudinary optimization, but pre-compression helps
- Consider using smaller thumbnails in collections

---

### Query Column Entry Limits

**What it is**: Query columns returning more than 10,000 entries.

**Source**: [Query Column Limits](https://help.glideapps.com/en/articles/12063345-understanding-query-column-limits-in-glide-apps)
> "The recommended limit for a query column in Glide Apps should not exceed 10,000 entries"

**Detection**: Check Query column configurations for tables with >10,000 rows.

**Fix**:
- Add filters to reduce result set
- Break large tables into smaller subsets
- Use Big Tables as query target (handles large data better)

---

### Big Table Aggregation Limits

**What it is**: Rollups, Lookups, or Joined Lists on Big Tables returning more than 100 matches.

**Source**: [Big Tables](https://www.glideapps.com/docs/big-tables)
> "Rollups and Joined Lists: Work with most computed columns but are limited to 100 matching rows"

**Detection**:
- Identify Big Tables in the app
- Check Rollups/Lookups that target Big Tables
- Estimate how many rows might match

**Fix**:
- Pre-aggregate data via workflows
- Restructure to reduce matches per rollup
- Accept the 100-row limit if it meets business needs

---

### Big Table Computed Column Filtering

**What it is**: Attempting to filter/sort by computed column types not supported in Big Tables.

**Background**: Big Tables use SQL (AlloyDB) for queries. Only computed columns that can be translated to SQL expressions work for filtering/sorting/rollups.

**Supported for filtering/sorting/rollups in Big Tables:**
- Math columns
- If-Then-Else columns
- Lookup columns (with restrictions - see below)
- Template columns (static template only)

**NOT supported for filtering/sorting/rollups in Big Tables:**
- Rollup columns
- Multi-relation columns
- Query columns
- Plugin-based computed columns

**Detection**:
- Identify Big Tables in the app
- Check if filters/sorts use unsupported computed column types
- Check collection configurations targeting Big Tables

**Lookup restrictions in Big Tables:**
- Must use a single relation (not multi-relation)
- The relation column must be a basic (non-computed) column
- Target table must also be a Big Table
- Target column must be a basic (non-computed) column
- Target column cannot be user-specific

**Template restrictions in Big Tables:**
- Template string must be static (constant text)
- Dynamic templates (where the template itself is computed) not supported

**Fix**:
- For filtering by Rollup values: Pre-calculate and store in basic column via workflow
- For multi-relation filtering: Restructure to use single relation or native Glide Tables
- For Query column filtering: Consider using native Glide Tables for that data
- For complex filters: Move logic to basic columns updated by workflows

---

## Layout Considerations

### Heavy First Screen

**What it is**: First screen/tab with many components or data.

**Source**: [Performance Tips](https://community.glideapps.com/t/app-performance-tips-tricks-speed/73139)
> "Keep first tab/screen lightweight to improve initial app load perception"

**Detection**: Check first tab in Layout Editor for complexity.

**Fix**:
- Move heavy content to secondary tabs
- Use simple components on first screen
- Delay-load complex data

---

### External Data Sources

**What it is**: Using Google Sheets, Airtable, or other external sources instead of Glide Tables.

**Source**: [Build for Speed and Scale](https://www.glideapps.com/blog/build-speed-and-scale)
> "Glide Tables load faster than external sources like Google Sheets or Airtable"

**Source**: [Performance Tips](https://community.glideapps.com/t/app-performance-tips-tricks-speed/73139)
> "Prefer Glide Tables over external sources"

**Detection**: Check Data tab for external source connections.

**Fix**:
- Migrate to Glide Tables for real-time data needs
- Keep external sources for data that syncs periodically
- Use Big Tables for large datasets

---

## What We Don't Know

Glide doesn't publish specific thresholds for:
- Number of relations per table
- Number of rollups per table
- Computed column chain depth limits
- Number of AI columns
- Screen component counts

For these, use judgment based on:
- Performance Analysis results (the 100ms warning)
- User-reported slowness
- App complexity and data volume

The best indicator is always **Glide's Performance Analysis tool** - if a column shows ⚠️, it needs attention regardless of what pattern it follows.
