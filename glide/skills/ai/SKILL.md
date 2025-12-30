---
description: |
  How to add AI features to Glide apps using Glide AI columns.
  Use this skill when adding AI-powered functionality to apps.
---

# Glide AI

Glide has powerful AI columns that run inference on your data. These can add significant value to apps with minimal effort.

## AI Column Types

| Column | Input | Output | Use Case |
|--------|-------|--------|----------|
| **Generate Text** | Text/template | AI-generated text | Summaries, descriptions, recommendations |
| **Image to Text** | Image URL | Extracted text | Receipt scanning, document OCR, product analysis |
| **Document to Text** | Document URL | Extracted/summarized text | PDF parsing, document processing |
| **Audio to Text** | Audio URL | Transcription | Voice notes, meeting recordings |
| **Text to Boolean** | Text | true/false | Sentiment analysis, spam detection |
| **Text to Choice** | Text + options | Selected option | Auto-categorization, priority assignment |
| **Text to JSON** | Text | Structured JSON | Entity extraction, form parsing |

## AI Techniques

### Detail Screen Summary with Hint Component

A powerful pattern: show an AI-generated summary at the top of detail screens.

How it works:
1. **Aggregate data** - Create a Template column or JSON Object column that combines relevant fields
   - Template: `Name: {Name}, Status: {Status}, Due: {Due Date}, Notes: {Notes}`
   - Or use JSON Object to structure the data
2. **Generate summary** - Create a Generate Text column that takes the aggregated data
   - Prompt: "Summarize this task in 1-2 sentences, highlighting priority and next steps"
3. **Display with Hint** - Add a Hint component at the top of the detail screen
   - Bind it to the Generate Text column
   - Users see an instant AI summary of the item

Example prompts:
- Task: "Summarize this task, noting urgency and blockers"
- Customer: "Provide a brief profile of this customer based on their activity"
- Order: "Summarize this order status and any issues"
- Project: "Give a quick health check of this project"

This gives users instant context without reading through all fields.

### Auto-Categorization with Text to Choice

Automatically categorize items as they're added:

1. Create a Text to Choice column
2. Define the categories (e.g., "Bug", "Feature", "Question", "Other")
3. Point it at the text field to analyze (e.g., ticket description)
4. New items get auto-categorized

Great for:
- Support tickets → category
- Feedback → sentiment (Positive, Negative, Neutral)
- Expenses → expense type
- Leads → qualification level

### Smart Descriptions from Basic Info

Generate rich descriptions from minimal input:

1. User enters basic info (name, a few keywords)
2. Template column combines the inputs
3. Generate Text creates a full description

Example for products:
- Input: "Blue widget, small, outdoor use"
- Generate Text prompt: "Write a compelling 2-sentence product description"
- Output: "Introducing our compact Blue Widget, perfectly sized for any outdoor adventure. Built to withstand the elements while delivering reliable performance wherever you go."

### Receipt/Document Scanning

Extract structured data from images:

1. User uploads a receipt photo
2. Image to Text extracts the content
3. Text to JSON parses into structured fields (vendor, amount, date)
4. Display parsed data in the UI

Works for:
- Receipts → expense tracking
- Business cards → contact creation
- Documents → data entry
- Product photos → inventory details

### Sentiment Analysis

Detect sentiment in text:

1. Create a Text to Boolean column
2. Prompt: "Is this feedback positive?"
3. Use result to show 😊 or 😞 emoji via If-Then-Else
4. Or use Text to Choice for Positive/Neutral/Negative

Apply to:
- Customer feedback
- Support ticket tone
- Review analysis
- Survey responses

## Best Practices

- **Aggregate before generating** - Combine relevant data into one input for better AI results
- **Be specific in prompts** - Tell the AI exactly what format and length you want
- **Use for summaries** - AI excels at condensing information
- **Cache results** - AI columns compute once and store the result
- **Consider cost** - AI columns use Glide AI credits; use strategically

## When to Add AI

Look for opportunities where:
- Users need to quickly understand complex data
- Manual categorization is tedious
- Text input could be richer/more helpful
- Images contain data that should be structured
- Summaries would save users time
