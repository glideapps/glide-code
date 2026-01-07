# Data Editor Selectors

Common elements in Glide's Data Editor.

## Left Sidebar - Data Sources

| Element | Description | Finding Strategy |
|---------|-------------|------------------|
| Data Sources section | List of tables | Heading "Data Sources" |
| Team name | Links to Big Tables | Team name text (click to link tables) |
| Table items | Individual tables | Table names in list |
| Selected table | Currently active | Highlighted/selected table |

## Table View - Header

| Element | Description | Finding Strategy |
|---------|-------------|------------------|
| Table name | Current table | Large text at top |
| Show API button | Opens API panel | Text "Show API" |
| Add row button | Creates new row | "+" or "Add row" |
| Column headers | Column names | Header row cells |

## Column Operations

| Element | Description | Finding Strategy |
|---------|-------------|------------------|
| Column header | Click for menu | Header cell with column name |
| Column menu | Options dropdown | Menu after header click |
| Add column right | Insert column | Text "Add column right" |
| Delete column | Remove column | Text "Delete" in menu |
| Edit column | Modify settings | Text "Edit" in menu |

## Column Creation Panel

After keyboard shortcut (Cmd+Shift+Enter) or "Add column":

| Element | Description | Finding Strategy |
|---------|-------------|------------------|
| Name input | Column name | Input with placeholder "Column name" or "Name" |
| Type dropdown | Column type | Button showing type name or "Type" |
| Type search | Filter types | Search input in type picker |
| Type options | Available types | Items in type dropdown |
| Save button | Create column | Text "Save" or "Done" |
| Cancel button | Abort creation | Text "Cancel" |

## Type Picker Categories

| Category | Description | Common Types |
|----------|-------------|--------------|
| Basic | Storage types | Text, Number, Boolean, Date, Image, URL |
| Computed | Calculations | Relation, Lookup, Rollup, Math, If-Then-Else, Template |
| AI | AI processing | Generate Text, Image to Text, Text to Choice |
| Integrations | External services | Call API, Claude, OpenAI, Google Maps |

## Relation Configuration

When configuring a Relation column:

| Element | Description | Finding Strategy |
|---------|-------------|------------------|
| Table dropdown | Target table | Dropdown labeled "Table" |
| Match source | Source column | Dropdown for "Match" |
| Match target | Target column | Dropdown for "With" |
| Single/Multiple | Cardinality | Toggle or radio buttons |

## Rollup Configuration

When configuring a Rollup column:

| Element | Description | Finding Strategy |
|---------|-------------|------------------|
| Relation dropdown | Source relation | Dropdown for relation column |
| Column dropdown | Column to aggregate | Dropdown for source column |
| Aggregation | Sum/Count/Avg/etc. | Dropdown for operation |

## API Panel

After clicking "Show API":

| Element | Description | Finding Strategy |
|---------|-------------|------------------|
| Code examples | Sample code | Code blocks |
| Token area | API token | Token text or masked string |
| Copy button | Copy token | Text "Copy" near token |
| Close button | Close panel | X button or "Close" |

## Table Data Area

| Element | Description | Finding Strategy |
|---------|-------------|------------------|
| Data grid | Table cells | Grid of data |
| Row cells | Individual values | Cells in rows |
| Empty row | Add new data | Row at bottom |
| Cell editor | Edit cell | Appears on cell click |

## Link Tables Panel

After clicking team name in Data Sources:

| Element | Description | Finding Strategy |
|---------|-------------|------------------|
| Available tables | Big Tables list | Table items with checkboxes |
| Link button | Link selected | Text "Link Table" |
| Search | Filter tables | Search input |
| Apps Linked | Usage count | Column showing app count |
