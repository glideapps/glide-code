---
name: design
description: |
  Design and build Glide screens, components, forms, and actions. Create screens that are intuitive, data-dense, and serve user workflows.
  Use when creating or designing screens, choosing components, building forms, configuring navigation, or reviewing and improving screen usability.
---

# Glide Layout & Design

## Screen Types

### Creating Screens

Click the "+" button in the Navigation or Menu section of the Layout Editor.

| Screen Type | Description | Use Case |
|-------------|-------------|----------|
| **Screen from data** | Collection screen linked to a table | Lists, directories |
| **Custom screen** | Blank screen to build freely | Dashboards, custom layouts |
| **Form screen** | Data entry form | Add/edit records |

**Important - Avoid Sample Screen Templates:**

When creating a new screen, you'll see options for "Sample Screens" templates (Project management, Dashboard, Company directory, etc.). **Do not use these templates** - they create pre-configured layouts that make it difficult to integrate your own data.

**Why avoid sample screens:**
- They come with sample/placeholder data that's hard to replace
- Data binding is pre-configured and difficult to modify
- Components may not match your actual data structure
- Harder to customize than building from scratch

**Recommended approach instead:**

1. **For collection screens (lists, directories):**
   - Choose "Screen from data"
   - Select your backing table
   - Design the collection and detail views from scratch with appropriate components

2. **For dashboards or multi-section screens:**
   - Choose "Custom Screen"
   - Optionally click "Choose data source" to link to a table
   - Add components directly to build your layout

3. **For all screens:**
   - Start blank, add components one by one
   - This gives you full control over data binding and layout
   - It's clearer what each component does and where data comes from

**Example workflow for a dashboard:**
```
1. Click "+" in Navigation
2. Select "Custom screen"
3. Click "Choose data source" if needed (optional - links to a table)
4. Add components: Headline, Big Numbers, Collections, Charts, etc.
5. Configure each component's data binding
6. Organize with Containers for multi-column layouts
```

This approach is more work upfront but results in screens that are easier to understand, modify, and debug.

## Design Principles

Keep these in mind when building and reviewing screens:

- **Show don't hide** - Important data should be visible at a glance, not buried in detail views
- **Reduce clicks** - Can users accomplish tasks with fewer taps? Minimize navigation
- **Context matters** - Group related information together so users see what they need
- **Mobile vs Desktop** - Optimize for how the app will actually be used (phone-first usually)
- **Progressive disclosure** - Show overview first, details on demand

## Viewing Layouts for Design Review

When reviewing or building screens in the Layout Editor, **always switch to Desktop preview** unless specifically designing a mobile-only app.

### Switch to Desktop Preview

1. Look for the **device switcher** in the Layout preview toolbar (shows phone/tablet/desktop icons)
2. Click the **Desktop** icon to see the full-width layout
3. This reveals how components fill horizontal space and how multi-column layouts render

Mobile preview is narrow and hides important layout issues:
- Tables truncate columns
- Multi-column containers collapse
- Side-by-side layouts stack vertically
- Cards show fewer per row

### See Below the Fold

To review full page layouts:

- **Scroll the preview** - Drag or scroll within the preview pane to see content below the fold
- **Zoom out browser** - Use Cmd/Ctrl + minus (-) to zoom out and see more of the page at once
- **Resize browser window** - Make the browser window taller to see more content

Always check:
- What users see on first load (above the fold)
- How the full screen flows when scrolled
- Whether important actions are visible or buried

## Screen Design Workflow

Each tab in a Glide app has multiple screens that need to be designed. **You must navigate to each screen in the Layout Editor to design it** - they don't appear automatically.

### Screens to Design for Each Tab

1. **The tab itself** - Set its label (1-2 words max) and icon in the Navigation section
2. **Collection screen** - The top-level screen showing the list/grid/table of items
3. **Detail screen** - Click an item in the preview to navigate to and design the detail view
4. **Edit screen** - If editing is enabled, navigate to the edit screen to design the form
5. **Add/New screen** - If adding is enabled, navigate to the add screen to design the new item form

### How to Navigate to Each Screen

In the Layout Editor:

1. **Collection screen**: Select the tab in Navigation - this is the default view
2. **Detail screen**: Click any item in the preview panel - the Layout Editor switches to show the detail screen's components
3. **Edit screen**: On the detail screen, click the Edit button/action in the preview - this navigates to the edit form
4. **Add screen**: On the collection screen, click the Add/+ button in the preview - this navigates to the add form

**Important**: The component list in the left sidebar changes based on which screen you're viewing. Make sure you're on the correct screen before adding or configuring components.

### Design Each Screen Thoughtfully

Don't just design the collection screen and leave the rest as defaults:

- **Detail screens** should be rich and informative - use Title components, organize with Containers, add inline collections for related data
- **Edit/Add forms** should be well-organized - group related fields, use appropriate input types, add helpful hints
- **Collection screens** should use the right style for the data (Table, Card, List, etc.)

Each screen type deserves attention. Users will interact with all of them.

### Replace Default Components

When you create a "Screen from data", Glide adds generic default components. **Delete these defaults and design the screen yourself** using components appropriate for the data.

**Why replace defaults:**
- Default components are generic and don't leverage Glide's rich component system
- They miss opportunities for better UX (Contact buttons for people, Maps for locations, Charts for metrics)
- They don't create visual hierarchy or organization
- The app looks like a template instead of a custom-built solution

**How to replace:**
1. Navigate to the screen (collection, detail, edit, or add)
2. Select and delete the default components in the left sidebar
3. Add components that match the data and use case
4. Organize with Containers, add visual interest with Title components

**Examples of better component choices:**

| Data Type | Default | Better Choice |
|-----------|---------|---------------|
| Employee with email/phone | Fields component | **Contact** component (tap-to-call/email buttons) |
| Address/location | Text field | **Map** component or **Location** component |
| Numeric KPIs | Fields component | **Big Numbers** component |
| Progress/completion | Number field | **Progress** bar component |
| Person with photo | Image + text | **Profile** title component |
| Project with banner | Image + text | **Cover** title component |
| Related items | Hidden or single field | **Inline Collection** showing all related records |
| Status field | Text | **Headline** with emoji from If-Then-Else column |
| Long description | Text field | **Rich Text** component |
| Multiple metrics | Multiple fields | **Container** with side-by-side Big Numbers |

**Detail screen example - Employee:**

Instead of default Fields component showing all columns:
1. **Profile** title - photo, name, job title
2. **Contact** component - email and phone with tap actions
3. **Location** component - office address
4. **Container** with:
   - **Headline** "About"
   - **Rich Text** - bio/description
5. **Container** with:
   - **Headline** "Team"
   - **Inline Collection** - other employees in same department

This creates a polished, purposeful screen instead of a data dump.

## Collection Styles

**This is one of the most important design decisions.** The collection style determines how users interact with your data. Always evaluate whether the current style is optimal for the task.

### Available Styles

| Style | When to Use |
|-------|------------|
| **Card** | Visual layout with images, photos, or rich metadata. Good for browsing, shows multiple fields per item |
| **List** | Compact and scannable. Shows 2-3 key fields. Best for simple data where users need to scan quickly |
| **Table** | Data-dense, columnar format. Excellent for comparing values across many rows (prices, dates, quantities) |
| **Data Grid** | Editable table. Like Table but allows inline editing of data |
| **Checklist** | For boolean/checkbox fields. Great for task lists and to-dos |
| **Calendar** | For date-based data. Shows events on a timeline or calendar grid |
| **Kanban** | For status/workflow data. Organize items by column (e.g., To Do → In Progress → Done) |
| **Custom** | Build your own layout for specialized use cases |

### How to Choose the Right Style

Ask yourself these questions:

1. **What task are users trying to accomplish?**
2. **Do they need to compare values across items?** → Use Table or Data Grid
3. **Do they need visual context for each item?** → Use Card
4. **Do they need to scan and find quickly?** → Use List
5. **Is there a workflow or status progression?** → Use Kanban or Checklist
6. **Are items date-based?** → Use Calendar

### Selection Checklist

- **Lots of comparable numeric data** (prices, quantities, percentages, dates, status codes) → **Table** or **Data Grid**
- **Rich visual information** (images, thumbnails, avatars, descriptions) → **Card**
- **Quick name/title scanning** (directories, simple lists) → **List**
- **Task management with workflows** (prioritize, move through stages) → **Kanban** or **Checklist**
- **Calendar/schedule focused** (events, appointments, dates) → **Calendar**
- **Need to edit inline** (quick updates, bulk changes) → **Data Grid**

### Design Review Tips

When evaluating collection style:
- If a card collection wastes space showing only one field, consider switching to List
- If a list is hard to scan because values are cut off, consider Table
- If items have rich metadata (images, descriptions), ensure Card is showing the relevant fields
- Remember mobile vs desktop - some styles work better on each (Tables are harder on mobile)
- Ask the user: "Would you ever need to compare values side-by-side?" If yes, Table is better

### Configuring Collections

**Every collection needs proper configuration** - don't just add a collection and leave it with default settings. The Options tab and display fields are critical for creating a useful, well-organized collection.

#### Options Tab - Filter and Sort

**Filter and Sort options are used on basically every collection.** Access them in the Options tab when you have a collection selected.

**Sort settings:**
- Configure what field to sort by and the sort order (A→Z or Z→A)
- **For time-based data**: Use descending order (Z→A) to show most recent items first
  - Example: Sort by "Created on:" field, Z→A order → newest problems appear at the top
- **For alphabetical data**: Use A→Z for standard alphabetical ordering
- **For numeric data**: Consider whether users want highest-to-lowest or lowest-to-highest

**Filter settings:**
- Limit what items appear in the collection based on conditions
- Example: Show only "Active" status items, or items from the past 30 days
- Use filters to create focused views of your data

**Common pattern for time-based collections:**
```
Collection: Recent Problems
Options tab:
  - Sort by: Created on
  - Order: Z→A (descending - most recent first)
  - Filter: (optional - e.g., only show last 5, or only active items)
```

#### Display Fields - Meta, Title, Description

**Think carefully about which columns to show in each display field** - this creates the visual hierarchy and determines what information users see at a glance.

**The three display fields:**

| Field | Visual Appearance | Use For | Example |
|-------|------------------|---------|---------|
| **Meta** | Small text above the title | Category, type, or context | "Bug Report", "Feature Request", "High Priority" |
| **Title** | Large, bolded text | Primary identifier or name | "Login page crashes on mobile", "Employee Name" |
| **Description** | Paragraph text below title | Details or summary | Full problem description, bio, address |

**Guidelines:**

1. **Meta field** - Short contextual info that helps categorize or identify the type
   - Status ("Open", "Closed")
   - Category ("Support", "Sales")
   - Type ("Bug", "Feature")
   - Priority ("High", "Medium", "Low")

2. **Title field** - The main identifier users will recognize
   - Name column
   - Subject or headline
   - Primary key field
   - Should be concise and unique enough to identify the item

3. **Description field** - Supporting details that help users understand the item
   - Full description or notes
   - Address or location
   - Summary text
   - Bio or about section
   - Can be longer than Title

**Example configuration for Problems collection:**
```
Collection style: Card or List
Display fields:
  - Meta: Type ("Bug Report", "Feature Request")
  - Title: Name ("Login page crashes on mobile")
  - Description: Description (full explanation of the problem)
Options:
  - Sort by: Created on, Z→A (most recent first)
```

**Best practices:**
- Don't leave display fields empty - choose the most relevant columns for each
- Match the field content to its visual weight (big bold Title for important info, small Meta for context)
- Think about what users need to see to identify and understand each item
- Consider adding emoji or icon columns for Meta to create visual scanning patterns

#### Collection Titles

**Use the built-in Title field** in the collection's Data properties, NOT a separate Text component above the collection.

**Why:**
- The Title field displays inline with the search bar and action buttons
- Creates a more professional, integrated UI
- Saves vertical space
- Follows Glide's design patterns

**How to set:**
1. Select the collection component
2. In the right properties pane, find the Data section
3. Set the Title field with your collection name (e.g., "Problems", "Inventory Locations")

**Don't do this:**
```
❌ Add a Text or Headline component above the collection
❌ Use a separate title component for the collection name
```

**Do this instead:**
```
✅ Use the Title field under Data in the collection's properties
✅ The title appears inline with search bar and buttons automatically
```

#### Removing Image Placeholders

**If your source table doesn't have images, remove dynamic data bindings from the Image field under Items Data.** This prevents blank grey squares from appearing in your collections.

**The problem:**
- When you bind the Image field to a column that has no image data
- Glide shows a grey placeholder square for each item
- This wastes space and looks unprofessional

**The solution:**
1. Select the collection component
2. Find Items Data section in the right properties pane
3. Locate the Image field
4. If it's bound to a column (e.g., "Type", "Meeting Choice", etc.) and that column has no actual images
5. Clear the binding (remove the dynamic data)
6. The grey squares disappear

**When to remove image bindings:**
- The source table has no image column
- The bound column contains text, not image URLs
- You see grey placeholder squares in the collection

**When to keep image bindings:**
- The source table has actual images (URLs, uploaded files, generated images)
- You're using profile photos, product images, or other visual content
- The images add value to the browsing experience

#### Filter Popup UI - Critical Behavior

**IMPORTANT: The X button on filter popups DELETES the filter, it is NOT a close button.**

This is a common point of confusion with significant consequences - clicking the X will remove your entire filter configuration, not save and close it.

**The problem:**
- Most UIs use X buttons to close dialogs and save changes
- Glide's filter popup uses X to DELETE/REMOVE the filter
- This is counterintuitive and can cause you to lose configured filters

**The correct way to save and close a filter popup:**
1. Configure your filter settings (conditions, matching rules, etc.)
2. **Click anywhere else on the screen** to close the popup
3. This saves the filter and closes the popup
4. The filter is now active and saved

**What NOT to do:**
- ❌ Don't click the X button expecting it to save and close
- ❌ The X button will delete the entire filter configuration

**When to use the X button:**
- Only when you want to DELETE/REMOVE the filter completely
- If you accidentally configured a filter you don't want

**Example workflow - Adding a filter to show only current client's visits:**
```
1. Select the Visits collection component
2. Click the Filter option in the Options tab
3. Configure the filter: "Client is 🔒 Row ID"
4. **Click elsewhere on the screen** (not the X) to close and save
5. ✅ Filter is now saved and active
```

**If you accidentally click X:**
- The filter will be deleted
- You'll need to reconfigure it from scratch
- Use CMD+Z immediately to undo if this happens

## Design Techniques

### Status Emojis with If-Then-Else Columns

Add color and visual interest using If-Then-Else computed columns to display status emojis. Users can scan status instantly.

Examples:
- Task status: ✅ Complete, 🔄 In Progress, ⏳ Pending, 🚨 Overdue
- Priority: 🔴 High, 🟡 Medium, 🟢 Low
- Approval: ✅ Approved, ❌ Rejected, ⏳ Pending
- Health: 💚 Good, 💛 Warning, ❤️ Critical
- Rating: ⭐⭐⭐⭐⭐ (chain multiple if-then-else)

How to implement:
1. Create an If-Then-Else column
2. Set conditions based on status/value
3. Return the appropriate emoji
4. Display in collection cards, titles, or badges

This adds instant visual scanning without taking up much space.

### Hero Icons Column (Experimental)

Use the Hero Icons experimental column to generate dynamic icons by name from the Hero Icons library. These render as images you can display anywhere.

How to use:
1. Add a Hero Icons column (under Experimental)
2. Set the icon name (e.g., "check-circle", "exclamation-triangle", "user")
3. The column outputs an image URL you can bind to Image components

Combine with If-Then-Else for dynamic icons:
1. Create an If-Then-Else column that returns icon names based on status
2. Feed that into a Hero Icons column
3. Display the resulting icon in your UI

Examples:
- Category icons: "folder", "document", "photo", "music"
- Action indicators: "arrow-right", "plus", "pencil", "trash"
- Status icons: "check-circle", "x-circle", "clock", "exclamation-circle"

Browse available icons at: https://heroicons.com

### Inline Collections for Multi-Relations

Display multi-relation columns as inline collections on detail screens to improve browsability.

Example: Office detail screen
- Offices table has a relation to Employees (one office → many employees)
- On the Office detail screen, add a Collection component
- Bind it to the Employees relation column
- Users can now see and browse all employees in that office without leaving the screen

This pattern works great for:
- **Parent → Children**: Project → Tasks, Customer → Orders, Category → Products
- **Location → People**: Office → Employees, Department → Staff, Team → Members
- **Container → Items**: Folder → Documents, Playlist → Songs, Cart → Items

How to implement:
1. Ensure you have a Relation column linking the tables
2. On the detail screen, add a Collection component
3. Set the collection's data source to the relation column
4. Choose an appropriate style (List, Cards, Table, etc.)

This lets users drill down into related data naturally, making the app feel more connected and explorable.

### Number Formatting

A subtle detail that makes apps look more polished: configure formatting on number and math columns.

Settings to check:
- **Decimal places**: Round to appropriate precision (0 for counts, 2 for currency)
- **Units**: Add prefix ($, €) or suffix (kg, mi, %)
- **Thousands separator**: Enable for large numbers (1,000 vs 1000)

Examples:
- Price: 2 decimals, $ prefix → "$29.99"
- Quantity: 0 decimals → "42"
- Percentage: 1 decimal, % suffix → "85.5%"
- Distance: 1 decimal, "mi" suffix → "3.2 mi"
- Weight: 2 decimals, "kg" suffix → "1.50 kg"

How to configure:
1. Click on the number/math column
2. Look for Format or Display settings
3. Set precision, prefix, suffix as needed

This small touch makes data instantly readable and professional.

## Screen Design Guidelines

### Screen Structure

**Collection screens** (the top level of most tabs):
- Show a collection (Card, List, Table, Kanban, or Calendar)
- Include filtering, sorting, or search if the data set is large
- Tap an item to drill into its detail screen

**Detail screens** (showing one item):
- Display the item's full information with well-chosen components
- Include edit and delete actions
- Show related data (inline collections for multi-relations)
- Should be carefully designed with nuance and visual interest

**Edit/Add screens** (data entry):
- Use form containers with organized form elements
- Group related fields logically
- Include validation and helpful hints
- Should be as thoughtful and polished as detail screens

### Building Rich Screens

Glide has a beautiful, diverse component system. Effective screens use it well:

**Component Variety**: Don't rely on just Fields components. Mix in:
- **Title components** (Cover, Profile, Image) for visual hierarchy
- **Content components** (Big Numbers, Progress, Charts) to visualize data
- **Text components** (Headline, Rich Text) for context and explanations
- **Action components** (Buttons, Links) for user interactions
- **Layout components** (Containers, Separators, Spacers) for organization

**Screen Density**: A well-designed screen typically has **5-15 components**. This provides:
- Enough information to be useful without overwhelming
- Room for visual breathing and hierarchy
- Opportunity to highlight what matters most

**Multi-Column Layouts**: Use **Containers** to create sophisticated layouts:
- Side-by-side columns for related information
- Left sidebar for navigation, main area for content
- Grid-like layouts for metrics and stats
- Cards within containers for modular designs

**Table Design**: Tables are data-dense and beautiful when designed thoughtfully:
- Add status emoji columns (If-Then-Else) for visual scanning
- Use icon columns (Hero Icons) for quick identification
- Format numbers properly (decimals, units, thousands separators)
- Add color-coded columns to highlight important values
- Include action columns (Edit, Delete) for direct manipulation
- Consider hiding less-important columns on mobile

### Going the Extra Mile

**Add nuance and interest**:
- Use status indicators (emojis, icons) throughout
- Add visual hierarchy with Headline components
- Include helpful Hint or Rich Text components for context
- Use Separators to group related sections
- Leverage color and styling options in component settings

**Maximize the component system**:
- Every screen should feel polished, not default
- Think about what would delight the user on this screen
- Use images, avatars, or cover photos when relevant
- Consider charts and visualizations for numeric data
- Add descriptive text, not just raw data
- Include progress indicators or status badges
- Make actions visible and easy to discover

**Example**: A detail screen showing a project might include:
- Cover image or Profile title with project name
- Headline with status emoji
- Rich Text describing the project
- Big Numbers showing key metrics (budget, timeline)
- Progress bar for completion
- Inline collection of related tasks
- Container with edit/delete buttons
- Separate container with team members
- Charts showing project breakdown

This approach transforms a basic data display into an engaging, useful interface that users want to interact with.

## Component Categories

### AI Components
| Component | Description |
|-----------|-------------|
| **Custom** (Beta) | AI-powered custom component |

### Title Components
| Component | Description |
|-----------|-------------|
| **Simple** | Basic title with text |
| **Image** | Title with image |
| **Profile** | User profile header |
| **Cover** | Full-width cover image |

### Collections
| Component | Description |
|-----------|-------------|
| **Card** | Card collection |
| **List** | List collection |
| **Table** | Table collection |
| **Data Grid** | Compact grid collection |
| **Checklist** | Checkable list |
| **Calendar** | Calendar view |
| **Kanban** | Kanban board |
| **Custom** | Custom collection |
| **Comments** | Comment thread |
| **Chat** | Chat interface |

### Layout Components
| Component | Description |
|-----------|-------------|
| **Container** | Group components together |
| **Separator** | Visual divider line |
| **Tabs Container** (Beta) | Tabbed content |
| **Spacer** | Empty space |

### Text Components
| Component | Description |
|-----------|-------------|
| **Text** | Display text |
| **Notes** | Note-taking component |
| **Rich Text** | Formatted text display |
| **Hint** | Helper text |
| **Headline** | Large heading |

### Content Components
| Component | Description |
|-----------|-------------|
| **Fields** | Display data fields |
| **Location** | Address/location display |
| **Image** | Image display |
| **Video** | Video player |
| **Big Numbers** | Large metric display |
| **Progress** | Progress bar |
| **Audio** | Audio player |
| **Audio Recorder** | Record audio |
| **Map** | Interactive map |
| **Bar Chart** | Bar graph |
| **Line Chart** | Line graph |
| **Chart** (Beta) | Flexible chart |
| **Radial chart** | Pie/donut chart |

### Action Components
| Component | Description |
|-----------|-------------|
| **Button Block** | Button with block styling |
| **Link** | Clickable link |
| **Action Row** | Row with action |
| **Rating** | Star rating |
| **Button** | Standard button |
| **Voice Transcription** (Beta) | Voice input |
| **Contact** | Contact buttons |

### Form Components
| Component | Description |
|-----------|-------------|
| **Contact Form** | Pre-built contact form |
| **Form Container** | Container for form fields |

### Form Elements
| Component | Description |
|-----------|-------------|
| **Text Entry** | Text input field |
| **Date Time** | Date and time picker |
| **Number Entry** | Number input |
| **Phone Entry** | Phone number input |
| **Email Entry** | Email input |
| **Switch** | Toggle switch |
| **Image Picker** | Image upload |
| **File Picker** | File upload |
| **Date** | Date picker |
| **Choice** | Dropdown/selection |
| **Checkbox** | Checkbox field |

### Advanced Components
| Component | Description |
|-----------|-------------|
| **Web Embed** (Explorer) | Embed external content |
| **Breadcrumbs** | Navigation breadcrumbs |
| **Scanner** (Business) | QR/barcode scanner |
| **Signature** | Signature capture |
| **Spinner** | Loading indicator |
| **Tabs** (Beta) | Tab navigation |

## Adding Components

1. Select a screen in the Layout Editor
2. Click "+" in the Components section (left sidebar)
3. Use the filter box to search or browse categories
4. Click a component to add it to the screen
5. Configure in the right panel

## Component Configuration

When a component is selected, the right panel shows:

### General Tab
- **Data binding**: Connect to columns
- **Label**: Display text
- **Visibility**: Show/hide conditions

### Options Tab
- Component-specific settings
- Styling options
- Advanced configuration

## Actions

Components can trigger actions on tap/click.

### Action Button Ordering

**Critical concept**: When you add multiple actions to a component, the order in the actions list determines the visual order of buttons in the UI.

**The rule**: **Top action in the list = Leftmost button in the UI**

**Example:**
```
Actions list order:        UI display order:
1. Edit                 →  [Edit] [Email Info]  (Edit is leftmost)
2. Email Info

Actions list order:        UI display order:
1. Email Info           →  [Email Info] [Edit]  (Email Info is leftmost)
2. Edit
```

**To reorder actions:**
1. Click and drag the action in the actions list
2. Move it above or below other actions
3. The UI button order updates automatically

**To make an action the primary button:**
- Drag it to the top of the actions list, or
- Remove other actions to leave only one

**Common use case**: Remove the default "Edit" action if you don't want users to edit on that screen, or move it below your custom actions to make your actions more prominent.

### Navigation Actions
- **Show New Screen** - Navigate to a new screen
- **Show Form Screen** - Open a form
- **Go to Tab** - Switch to a tab

### Data Actions
- **Add Row** - Create new record
- **Set Values** - Update data
- **Delete Row** - Remove record

### Other Actions
- **Open Link** - Open URL
- **Show Notification** - Display message
- **Copy to Clipboard** - Copy text
- **Compose Email/SMS** - Start message

## Visibility Conditions

Control when components appear:

**Important**: Visibility conditions are NOT security features. They only hide UI elements - the data is still downloaded. Use Row Owners for data security.

Example conditions:
- Column value equals/contains
- Current user matches
- Date comparisons

## Form Patterns

### Basic Form Screen
1. Create Form screen
2. Form Container is added automatically
3. Add form elements inside
4. Configure Submit action

### Inline Editing
1. Add form elements to detail screen
2. Bind to columns with write access
3. Changes save automatically

### Multi-Step Form
1. Use Multi-Step form template, or
2. Create Custom screen with multiple containers
3. Use visibility conditions to show one step at a time

## Layout Best Practices

1. **Use Containers** - Group related components
2. **Add Spacers** - Improve visual breathing room
3. **Consistent styling** - Use app appearance settings
4. **Mobile-first** - Design for phone, scales up
5. **Test different users** - Use "Viewing as" dropdown

## Screen Navigation Structure

### Navigation (Tab Bar)
- Screens shown at bottom on mobile
- Top nav on desktop (if Layout: Top)
- Limited space - keep to 3-5 screens

### Menu (Slide-out)
- Accessible via hamburger menu
- Good for secondary screens
- User Profile screen is here by default

### Nested Screens
- Created via Show New Screen action
- Not visible in main navigation
- Used for detail views, forms

## Documentation

- [Screens Overview](https://www.glideapps.com/docs/basics/screens)
- [Components Reference](https://www.glideapps.com/docs/basics/components)
- [Actions](https://www.glideapps.com/docs/actions)
- [Visibility Conditions](https://www.glideapps.com/docs/basics/visibility-conditions)
