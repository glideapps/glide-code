---
name: layout
description: |
  Glide screens, UI components, layouts, forms, and actions in the Layout Editor.
  Use when creating screens, adding components (Hero, Fields, Buttons), building forms, or configuring navigation.
---

# Glide Layout

## Screen Types

### Creating Screens

Click the "+" button in the Navigation or Menu section of the Layout Editor.

| Screen Type | Description | Use Case |
|-------------|-------------|----------|
| **Screen from data** | Collection screen linked to a table | Lists, directories |
| **Custom screen** | Blank screen to build freely | Dashboards, custom layouts |
| **Form screen** | Data entry form | Add/edit records |

### Sample Screen Templates

Pre-built templates for common patterns:
- **Project management** - Task tracking layout
- **Dashboard** - Overview with metrics
- **Company directory** - People/contacts list
- **Multi-Step form** - Wizard-style data entry
- **Chat** - Messaging interface

## Collection Styles

When creating a Screen from data, choose how to display the collection:

| Style | Description | Best For |
|-------|-------------|----------|
| **Card** | Card grid layout | Visual content, images |
| **List** | Vertical list | Text-heavy data |
| **Table** | Spreadsheet-like | Detailed data, editing |
| **Data Grid** | Compact grid | Large datasets |
| **Checklist** | Checkbox list | Tasks, to-dos |
| **Calendar** | Calendar view | Date-based data |
| **Kanban** | Board with columns | Status workflows |
| **Custom** | Build your own | Complex layouts |

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

Components can trigger actions on tap/click:

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
