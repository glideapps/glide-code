---
name: learnings-log
description: |
  Historical record of expert tips and learnings that have been integrated into the plugin skills.
  Consult this when patterns emerge or to understand the evolution of knowledge.
---

# Learnings Log

This skill tracks all successful expert tips that have been integrated into the plugin's knowledge base. Each entry records what was learned and which skills were updated.

## Purpose

- **Historical record**: See what tips have been provided
- **Pattern detection**: Identify common issues or knowledge gaps
- **Validation**: Verify learnings are being properly integrated
- **Evolution tracking**: Understand how the plugin's knowledge grows

## Log Format

Each learning entry should include:
- **Date**: When the tip was provided
- **Context**: What was stuck or going wrong
- **Tip**: The expert guidance provided
- **Skills Updated**: Which skill files were modified
- **Key Learning**: Summary of what was learned

## Learning Entries

### 2025-12-31: Row IDs Are the Proper Foundation for Relations

**Context**: Build agent tried to create a relation between Clients and Visits tables but couldn't match columns properly. The "Client Name" column in Visits actually contained Row IDs, not names, causing confusion about how to create the relation.

**Tip Provided**:
> "Client Name in Visits is the Row ID of the client, not the name. Every table has Row IDs which you should frequently use in relations. To enable, create a new column -> Change Type to Basic -> Row ID. You don't have to name this column, it will be auto-named Row ID"

**Skills Updated**:
- `data-modeling/SKILL.md` - Section: "Row IDs - The Foundation of Relations"
  - Added: New dedicated section explaining Row IDs as the foundation of relations
  - Added: Step-by-step process for creating Row ID columns (Basic → Row ID type)
  - Added: Why Row IDs are better than names (unique, stable, reliable, standard)
  - Added: "Common Pattern: Data Contains Row IDs" subsection
  - Added: Real-world example of Visits table referencing Clients via Row IDs
  - Added: When this pattern appears (forms, imports, ID references)
  - Updated: "Relation & Lookup Pattern" section with Step 0 (ensure Row ID columns exist)
  - Added: Specific example of matching Row ID columns in relations
  - Enhanced: Step 2 explanation showing how Lookups bring human-readable data
- `computed-columns/SKILL.md` - Section: "Relation Columns"
  - Added: "Best Practice: Use Row IDs" subsection at top of Relation section
  - Added: Detailed explanation of why Row IDs are the proper approach
  - Added: "Creating Row ID Columns" subsection with step-by-step instructions
  - Added: "Common Pattern: Data Contains Row IDs" subsection
  - Added: Example showing Visits referencing Clients via Row IDs
  - Added: When this pattern appears (forms, imports, ID fields)
  - Added: "Why Not Match on Names?" subsection
  - Added: Clear guidance on why name-based relations fail (duplicates, changes, exact matches)

**Key Learning**: Row IDs are the proper foundation for creating relations in Glide. Every table should have a Row ID column (Basic → Row ID type, auto-names itself "Row ID"). When data contains Row IDs (like a "Client" column with values like "abc123"), match it to the Row ID column in the related table, then use Lookup columns to display human-readable information.

**Impact**: Establishes Row IDs as the standard pattern for table relationships. Prevents relations from breaking due to duplicate names, changing values, or exact-match issues. Makes it clear that imported data often contains Row IDs (not names) and how to properly create relations using them. Critical for building reliable, maintainable data models that don't break when data changes. This is a fundamental Glide pattern that applies to all apps with related tables.

---

### 2025-12-31: Table Creation via API is Primary Method

**Context**: Build agent was asked to create a new Glide app with tables (Clients and Visits), but didn't know the proper approach for creating tables when building apps.

**Tip Provided**:
> "when data isn't provided to you, you should determine a good data schema and use the data agent and the api to create the tables and add them to the app. This is the primary way you add tables."

**Skills Updated**:
- `glide/SKILL.md` - Section: "Create Tables via API"
  - Added: Clear statement that data agent + API is the primary method for table creation
  - Added: Explicit instruction to not attempt table creation through the UI
  - Added: Step-by-step workflow (Design schema → Use data agent → API creates tables → Build agent links them)
  - Added: Note that this applies both when importing from files and when creating new app concepts
- `data-modeling/SKILL.md` - New Section: "Table Creation Strategy"
  - Added: Complete conceptual overview of the table creation workflow
  - Added: Three-step process (Design schema, Use data agent, Don't use UI)
  - Added: Detailed breakdown of schema design considerations
  - Added: Explanation of why API is preferred (faster, consistent, validates with sample data)
  - Added: Note that UI column addition comes after tables exist

**Key Learning**: When building a new Glide app or adding tables to an existing app, the correct workflow is to design a data schema and use the data agent with the Glide API to create the tables. This is the primary method - don't try to create tables through the UI.

**Impact**: Establishes the fundamental table creation workflow that agents should follow. Prevents confusion about whether to use UI or API for table creation. Makes it clear that schema design → data agent → API is the standard pattern for all table creation tasks. Critical for efficient app building and avoiding manual UI table creation which is slower and less reliable.

---

### 2025-12-31: Collection Titles and Image Placeholders

**Context**: Building collections on the Armstrong portal page (problems and inventory locations). Issues were: (1) Using separate Text components above collections for titles instead of the proper Title field, and (2) Blank grey squares appearing where images didn't exist in source tables.

**Tip Provided**:
> "if we don't have an image in the source table, we should remove any dynamic data in the image option under Items Data. This will remove the blank grey square. Also instead of using Text above the collection for a title. The 'Title' under Data in the right properties pain of a collection adds a Title inline with the search bar and buttons"

**Skills Updated**:
- `design/SKILL.md` - Section: "Configuring Collections"
  - Added: "Collection Titles" subsection explaining the built-in Title field
  - Added: Why to use Title field (inline with search/buttons, professional UI, saves space)
  - Added: How to set the Title field in Data properties
  - Added: Examples of what NOT to do (Text/Headline components) vs what TO do (Title field)
  - Added: "Removing Image Placeholders" subsection
  - Added: Explanation of the grey square problem when binding Image field to non-image columns
  - Added: Step-by-step solution for clearing image bindings
  - Added: When to remove vs keep image bindings

**Key Learning**: (1) Collections have a built-in Title field (Data > Title) that displays inline with search bars and buttons - never use separate Text components for collection titles. (2) If source tables don't have images, remove dynamic bindings from the Image field under Items Data to prevent grey placeholder squares.

**Impact**: Creates more professional, integrated UIs by using Glide's native Title field. Eliminates visual clutter from grey placeholder squares. Ensures collections look polished and space-efficient. Critical for proper collection design patterns.

---

### 2025-12-31: Collection Configuration - Options Tab and Display Fields

**Context**: Created a problems collection on the Armstrong portal page, but it wasn't properly sorted to show the 5 most recent problems, and the display fields (Meta, Title, Description) weren't optimally configured.

**Tip Provided**:
> "In the options tab of a collection you will find Filter and Sort options. These are used on basically every collection. For this one, i mentioned i only wanted to see the 5 most recent problems so we should sort the table in reverse order. Any time you place a collection you think think about the data in that table and choose the best data to show in Title, Description, and Meta. Meta is a small wording above the title. Description shows as a paragraph under the title text. Title's are bolded in a collection"

**Skills Updated**:
- `design/SKILL.md` - Section: "Collection Styles"
  - Added: New "Configuring Collections" subsection after "Design Review Tips"
  - Added: "Options Tab - Filter and Sort" section with guidance on sort order strategies
  - Added: Specific note about using Z→A (descending) order for time-based data to show most recent first
  - Added: "Display Fields - Meta, Title, Description" section with detailed table showing visual appearance and use cases
  - Added: Guidelines for choosing which columns to display in each field (Meta for context, Title for primary identifier, Description for details)
  - Added: Complete example configuration for Problems collection
  - Added: Best practices for display field configuration

**Key Learning**: Every collection requires configuration in the Options tab (Filter and Sort - used on basically every collection) and thoughtful selection of display fields (Meta, Title, Description) to create proper visual hierarchy and data organization. For time-based data, use descending sort (Z→A) to show most recent items first.

**Impact**: Prevents collections from being left with default configuration. Ensures collections are properly sorted and display the most relevant information in the right visual hierarchy. Makes it clear that collection configuration is not optional - it's a critical step in creating useful, well-organized screens. Establishes clear guidance for the Meta/Title/Description pattern that appears throughout Glide.

---

### 2025-12-31: Avoid Sample Screen Templates - Start Blank Instead

**Context**: When building screens, the plugin was likely attempting to use Sample Screens from the new screen button, which create pre-configured templates that make integrating custom data difficult.

**Tip Provided**:
> "You should not use the Sample Screens from the new screen button. Those create samples which make getting your own data into rather difficult. You should start blank and add components to a screen. Many times you will use Custom Screen > Choose data source to fulfill this. In this scenario though you can just start adding components to the screen."

**Skills Updated**:
- `design/SKILL.md` - Section: "Creating Screens"
  - Replaced: Sample Screen Templates subsection with "Important - Avoid Sample Screen Templates" section
  - Added: Explanation of why Sample Screens are problematic (placeholder data, pre-configured binding, hard to customize)
  - Added: Recommended approach for different screen types (collection screens, dashboards, multi-section screens)
  - Added: Specific workflow examples for collection screens ("Screen from data") and dashboards ("Custom Screen")
  - Added: Example workflow showing Custom Screen > Choose data source > Add components pattern
  - Added: Note that starting blank gives full control over data binding and layout
- `glide/SKILL.md` - Section: "Build Screens"
  - Added: "Critical - Screen Creation Method" subsection after the main build steps
  - Added: Clear directive to never use Sample Screen templates
  - Added: When to use "Screen from data" vs "Custom Screen"
  - Added: Emphasis on starting blank and adding components for control

**Key Learning**: Sample Screen templates in Glide create pre-configured layouts with placeholder data that are difficult to customize. Always start with blank screens ("Screen from data" or "Custom Screen") and add components manually for full control over data binding.

**Impact**: Prevents frustration and wasted time trying to modify sample templates. Ensures screens are built with proper data binding from the start. Makes the app building process more predictable and debuggable. Critical for avoiding data integration issues that arise from template pre-configurations.

---

### 2025-12-31: Action Button Ordering and Multiple Actions

**Context**: Task required adding an email button to send location information. The Edit button was appearing as the primary (leftmost) button, and the new Email Info button was secondary. Needed to understand how action ordering works in Glide.

**Tip Provided**:
> "In 'Actions' anywhere in Glide the top 'action' is the left most button. So in the current app we have Edit at the top and edit is the main thing seen. If we don't need to be able to edit that page, we should just remove this button. Alternatively, you can move edit below another action to get the other action to appear first."

**Skills Updated**:
- `layout/SKILL.md` - Section: "Actions"
  - Created: New "Action Button Ordering" subsection at top of Actions section
  - Added: Core rule - top action in list = leftmost button in UI
  - Added: Visual example showing list order vs UI display order
  - Added: Step-by-step instructions for reordering actions via drag-and-drop
  - Added: Guidance on making an action primary (move to top or remove others)
  - Added: Common use case about removing/reordering default Edit action

**Key Learning**: In Glide's actions list, the top action appears as the leftmost button in the UI. To control button prominence, drag actions to reorder them or remove unwanted actions.

**Impact**: Prevents confusion about why buttons appear in unexpected order. Makes it clear how to prioritize custom actions over default actions like Edit. Essential for creating intuitive UIs with multiple action buttons.

---

### 2025-12-31: Send Email Action - Recipient Field Configuration

**Context**: Send Email action wasn't working because the To, Cc, and Bcc fields weren't configured correctly for the use case of emailing location information to the logged-in user.

**Tip Provided**:
> "Send Email action isn't working because the to, cc, and bcc field aren't configured correctly. For this case, use the user's email address as the To field and clear the others"

**Skills Updated**:
- `layout/SKILL.md` - Section: "Email Actions"
  - Added: New "Configuring Send Email action" subsection
  - Added: Detailed guidance for each recipient field (To, Cc, Bcc)
  - Added: How to send to logged-in user (User / Email binding)
  - Added: How to send to specific person (email column from data)
  - Added: Guidance on when to clear optional fields (Cc/Bcc)
  - Added: Common mistake note about leaving fields configured unnecessarily
  - Added: Complete example for emailing to logged-in user with cleared Cc/Bcc

**Key Learning**: Send Email actions require careful configuration of recipient fields. For sending to the logged-in user, use `User / Email` for the To field and clear Cc/Bcc if not needed.

**Impact**: Prevents Send Email actions from failing due to misconfigured recipient fields. Clarifies the difference between required (To) and optional (Cc/Bcc) fields. Makes it clear how to bind to the logged-in user's email address.

---

### 2025-12-31: Reordering Actions via Drag-and-Drop

**Context**: Needed to make the Email Info action the primary button by moving it above the Edit action in the actions list. Initial attempt didn't work because the method wasn't clear.

**Tip Provided**:
> "You still haven't ordered the actions correctly. You need to drag Email Info action above the 'Add Action' piece"

**Skills Updated**:
- `layout/SKILL.md` - Section: "Action Button Ordering"
  - Added: Specific instruction to drag actions to reorder them
  - Added: Note that actions can be moved above or below each other
  - Added: Clarification that UI button order updates automatically

**Key Learning**: To reorder actions in Glide, click and drag the action in the actions list to move it above or below other actions.

**Impact**: Makes the reordering mechanism explicit. Prevents confusion about how to actually change action order once you understand the top-to-bottom = left-to-right rule.

---

### 2025-12-31: Using CMD+Z to Undo Mistakes

**Context**: Accidentally deleted the Email Info action while trying to reorder actions. Needed to quickly restore it without rebuilding.

**Tip Provided**:
> "Stop you just deleted the email action, quick CMD+Z to get it back"

**Skills Updated**:
- `glide/SKILL.md` - Section: "Browser Automation Tips > Keyboard shortcuts"
  - Added: CMD+Z / CTRL+Z to keyboard shortcuts list
  - Created: New "Undo mistakes" subsection
  - Added: Explanation of Glide's undo functionality
  - Added: Common scenarios where undo is useful (deleted action, removed component, changed setting)
  - Added: Important note about undo only working for recent actions in current session

**Key Learning**: Glide Builder supports standard undo functionality with CMD+Z (macOS) or CTRL+Z (Windows) to revert recent changes.

**Impact**: Prevents need to rebuild components/actions that are accidentally deleted. Provides a safety net during browser automation. Critical for recovering from automation errors without starting over.

---

### 2025-12-31: Send Email vs Compose Email Actions

**Context**: Task required adding a button to send an email with location information. Initially selected "Compose email" action, but this would open the user's email app rather than send programmatically.

**Tip Provided**:
> "Send email uses Glide's built in email service to send emails. Compose email opens the users default email app to send an email. For most cases we want to use the Send Email action unless the user specifically asks to open their email app."

**Skills Updated**:
- `layout/SKILL.md` - Section: "Actions"
  - Created: New "Email Actions" section separating Send Email and Compose Email
  - Added: Detailed explanation of Send Email (Glide's built-in service, recommended default)
  - Added: Detailed explanation of Compose Email (opens user's email app, use only when explicitly requested)
  - Added: Use cases and examples for each action type
  - Added: Clear guidance on default choice (Send Email unless user specifically asks for email app)
  - Updated: "Other Actions" section to remove combined "Compose Email/SMS" entry, added separate "Send SMS" entry

**Key Learning**: "Send Email" and "Compose Email" are two distinct actions with different behaviors - Send Email uses Glide's service to send programmatically (recommended default), while Compose Email opens the user's email app (use only when explicitly requested).

**Impact**: Prevents confusion about which email action to use. Ensures automated email features work correctly without requiring user interaction. Makes the distinction clear between programmatic sending and manual composition.

---

### 2025-01-01: Initial Log Created

This learnings log was created as part of the self-learning `/tip` command feature.

**Purpose**: Enable the plugin to learn from expert feedback and continuously improve its knowledge base.

---

<!-- New learnings will be added below this line in reverse chronological order -->

### 2026-01-01: Query Columns for Per-Item Aggregations

**Context**: Build agent tried to create a "Total Visits" Rollup column to count visits per client, but mistakenly created a Rollup that counted ALL visits in the entire Visits table. This gave incorrect results where every client showed the same total (5 visits) instead of their individual visit counts.

**Tip Provided**:
> "Total Visits is wrong because you are counting the number of objects in the Visits table total. In order to get just that client's visits, you need to create a relation that relates the row ID to the Client column in the Visits table. Or better yet, use Query instead of relation and add a filter where the row ID of the Client matches the visits table client column. Sort the query by date. Then use Rollup to count the Query results (not the whole table), and use Single Value to get the first result of the sorted Query for last visit date. Query and relations are basically the same thing, except queries let you add more filters."

**Skills Updated**:
- `computed-columns/SKILL.md` - Section: "Query Columns" (NEW)
  - Added: Complete new section on Query columns after Rollup Columns
  - Added: "When to Use Query Instead of Basic Rollup" subsection explaining the problem
  - Added: Warning in Rollup section about not using Rollup directly on entire tables
  - Added: "Query → Rollup/Single Value Pattern" subsection with step-by-step approach
  - Added: Complete real-world example (Client Visit Statistics) showing Query → Rollup → Single Value → Math pattern
  - Added: "Why This Works" explanation with checkmarks showing the correct flow
  - Added: "Common Mistake" comparison showing wrong vs right approach
  - Added: "Query vs Relation" comparison table with similarities and differences
  - Added: Guidance on when to use Query vs Relation
  - Added: Query Configuration subsection detailing Source, Filter, Sort, and Limit options
  - Enhanced: "Single Value Columns" section with Query pattern example
- `data-modeling/SKILL.md` - Section: "Per-Item Aggregations: Query Pattern" (NEW)
  - Added: New section before "Relation & Lookup Pattern" for conceptual overview
  - Added: "The Problem: Rollup on Entire Table" subsection explaining common mistake
  - Added: "The Solution: Query → Rollup Pattern" with step-by-step approach
  - Added: Complete example showing Client Visit Statistics implementation
  - Added: Results showing different counts per client (Sarah: 1, Maria: 2, etc.)
  - Added: "Query vs Relation for Aggregations" subsection with use case guidance
  - Added: Expert insight quote about Query vs Relation capabilities

**Key Learning**: Don't use Rollup directly on an entire table when you need per-item aggregation. Instead, use the Query → Rollup/Single Value pattern: (1) Create a Query column to filter data to the current row (e.g., Visits where Client = Row ID), (2) Create Rollup or Single Value columns that reference the Query (not the whole table). This gives per-item statistics instead of table-wide totals.

**Impact**: Prevents a fundamental misunderstanding about how Rollup works. Makes it clear that Rollup aggregates whatever source you give it - if you give it an entire table, every row gets the same result. Query columns act as filters that create per-row subsets, which then can be aggregated correctly. This pattern is essential for common use cases like "count of orders per customer", "sum of expenses per project", "most recent activity per user", etc. Critical for building accurate, useful data models.

---

### 2026-01-01: Filter Popup X Button Deletes Filter

**Context**: Build agent was configuring a filter on the Visits collection to show only visits for the current client. After creating the filter ("Client is 🔒 Row ID"), the agent clicked the X button to close the popup, which accidentally deleted the filter instead of saving it.

**Tip Provided**:
> "the x on the side of the filter pop up deletes the filter and is not a close button. Try to create the filter exactly the same again but then to close click anywhere else on the screen to close the filter pop up box"

**Skills Updated**:
- `design/SKILL.md` - Section: "Configuring Collections"
  - Added: New "Filter Popup UI - Critical Behavior" subsection
  - Added: Clear warning that X button DELETES the filter (not a close button)
  - Added: Explanation of why this is counterintuitive (most UIs use X to close and save)
  - Added: Correct way to save and close filters (click anywhere else on screen)
  - Added: What NOT to do (don't click X expecting it to save)
  - Added: When to use X button (only when you want to DELETE the filter)
  - Added: Example workflow showing correct filter configuration and save process
  - Added: Recovery tip (use CMD+Z immediately if X is accidentally clicked)

**Key Learning**: The X button on Glide's filter popups DELETES the filter configuration - it is NOT a close button. To save and close a filter popup, click anywhere else on the screen.

**Impact**: Prevents accidental deletion of configured filters. This is a critical UX pattern that goes against standard UI conventions (X usually means "close"), making it easy to lose work. Explicitly documenting this behavior prevents frustration and wasted time reconfiguring filters. Essential for all collection filter configuration workflows.

---

### 2025-12-31: Keyboard Shortcut for Adding Columns

**Context**: Builder agent needed to add a 'created date' column to the locations table. Manual column creation via UI clicks can be slow and less reliable.

**Tip Provided**:
> "Whenever you need to add a column it will always need done via browser automation. You can navigate to a table and then use the keyboard shortcut CMD+SHIFT+ENTER to create a new column. This is much faster"

**Skills Updated**:
- `data-modeling/SKILL.md` - Section: "Creating Columns via UI"
  - Added: Keyboard shortcut method as the recommended approach (CMD+SHIFT+ENTER)
  - Added: Step-by-step workflow for keyboard shortcut method
  - Added: Explanation of why this method is preferred (faster, more reliable)
  - Reorganized: Manual UI method as alternative approach
- `glide/SKILL.md` - Section: "Data Editor > Adding Columns"
  - Added: Note that all column creation requires browser automation (API doesn't support it)
  - Added: Keyboard shortcut method as recommended approach
  - Updated: Emphasized that keyboard shortcut is significantly faster
- `glide/SKILL.md` - Section: "Browser Automation Tips > Keyboard shortcuts"
  - Added: CMD+SHIFT+ENTER shortcut to the keyboard shortcuts reference list

**Key Learning**: CMD+SHIFT+ENTER is the fastest and most reliable way to add columns in Glide when using browser automation.

**Impact**: Significantly speeds up column creation workflows. Makes automation more efficient and reduces reliance on fragile UI click sequences. This is especially valuable when adding multiple columns or when building apps with complex data models.

---

## Usage Notes

### For the skill-learner agent:

When you update skills based on a tip, also add an entry to this log:

```markdown
### YYYY-MM-DD: [Brief Title]

**Context**: [What was stuck or going wrong]

**Tip Provided**:
> [The user's tip in their own words]

**Skills Updated**:
- `skill-name/SKILL.md` - Section: [section name]
  - Added: [brief description of what was added]

**Key Learning**: [One-sentence summary of the core insight]

**Impact**: [Why this matters / what problem this prevents]

---
```

### For reviewing patterns:

Periodically review this log to identify:
- **Recurring themes**: Are similar tips being provided? Might indicate a gap in the main skills
- **Complex areas**: Which topics generate the most tips? May need better documentation
- **Success patterns**: What types of tips integrate most successfully?

### Example Entry

```markdown
### 2025-01-15: Emoji Picker Navigation

**Context**: Builder agent was stuck trying to select an app icon, kept failing to click the right emoji from the category view.

**Tip Provided**:
> "Don't browse the emoji categories. Always use the search box - type the emoji name like 'rocket' or 'checkmark' and click the result. It's much faster and more reliable."

**Skills Updated**:
- `glide/SKILL.md` - Section: "Creating New App"
  - Added: Detailed guidance on emoji picker search workflow
  - Added: Note about avoiding category browsing
- `layout/SKILL.md` - Section: "UI Navigation Patterns"
  - Added: General emoji picker usage pattern

**Key Learning**: Glide's emoji picker is optimized for search, not browsing - category navigation is unreliable for automation.

**Impact**: Prevents future failures when setting app icons or using emoji fields. Makes icon selection deterministic.

---
```

## Statistics

As tips accumulate, track:
- Total tips integrated: [number]
- Most frequently updated skills: [list]
- Common categories: [UI navigation, API usage, workflows, etc.]

## Integration with /tip Command

The `/tip` command automatically:
1. Calls skill-learner agent to update relevant skills
2. skill-learner agent adds entry to this log
3. User can review both skill updates and log entry

## Future Enhancements

Consider adding:
- Searchable index of learnings by topic
- Links from log entries to specific skill sections
- Rollback capability (revert a learning if it was incorrect)
- Export functionality to share learnings with other users
- Automatic pattern detection (analyze log for recurring themes)
