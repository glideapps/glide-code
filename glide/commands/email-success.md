---
name: email-success
description: Draft email responses to customer Glide questions - paste email content or check inbox automatically
---

# Email Success Command

Automate responding to customer questions about Glide. This command:
1. Analyzes the customer's question
2. Researches the answer using internal docs and external sources
3. Drafts a professional email response

**See `email-success-examples.md` for real-world before/after examples showing what great responses look like.**

## Usage Modes

### Mode 1: Paste Email Content (Default)

**When to use:** Quick, works immediately, no setup required

**Usage:**
```
/email-success

Then paste the email content when prompted.
```

### Mode 2: Browser-Based Email Checking

**When to use:** Check multiple emails, automated workflow

**Usage:**
```
/email-success --inbox gmail
or
/email-success --inbox outlook
```

### Mode 3: Email Hook (Future)

**When to use:** Fully automated forwarding workflow

**Coming soon:** Forward emails to a special address and get auto-responses

## How It Works

### Step 1: Get Customer Email

**Mode 1 (Paste):**
1. Ask user to paste email content
2. Parse sender, subject, and body
3. Extract the Glide-related question

**Mode 2 (Browser):**
1. Navigate to Gmail/Outlook
2. Check for unread emails in specific folder/label
3. Read most recent Glide-related question
4. Extract content

### Step 2: Analyze Question

Use the same logic as `/ask` command to:
1. Identify question type (viability, feature, planning, how-to, comparison)
2. Determine what knowledge is needed
3. Read relevant internal documentation

### Step 3: Research Answer

**Internal Sources (read first):**
```
glide/skills/glide/SKILL.md           ← Overall workflow
glide/skills/data-modeling/SKILL.md   ← Tables, columns
glide/skills/computed-columns/SKILL.md ← Formulas, calculations
glide/skills/design/SKILL.md          ← Screen design
glide/skills/workflows/SKILL.md       ← Automation
glide/skills/ai/SKILL.md              ← AI features
glide/skills/api/SKILL.md             ← API usage
glide/skills/app-sharing/SKILL.md     ← Privacy, auth
```

**External Sources (if needed):**
- Glide Docs: `site:glideapps.com/docs [query]`
- Glide Community: `site:community.glideapps.com [query]`
- Web search for recent features

### Step 4: Draft Email Response

Generate a professional email with:

**Email Structure:**
```
Subject: Re: [original subject]

Hi [name],

[Opening - acknowledge their question]

[Answer - clear, direct, helpful]

[Example/Screenshot - if helpful]

[Next steps - optional]

Let me know if you have any other questions!

Best,
[Your name/signature]
```

**Tone Guidelines:**
- **Keep under 200 words** - Brevity is valued
- **Limit to 2 alternatives max** - More choices = decision paralysis
- **Suggest paradigm shifts** - Don't just work around limitations, consider if there's a better approach
- **Add personal touches** - Holiday greetings, company updates, warmth
- **Save technical detail for follow-ups** - Start simple, elaborate if asked
- Professional but friendly
- Clear and actionable
- Use examples sparingly
- Minimal links (only if essential)
- Offer follow-up help

### Step 5: Present Draft

Show the drafted email to the user with:
1. **Email preview** - Full draft
2. **Research notes** - What sources were used
3. **Confidence level** - How certain we are about the answer
4. **Suggested attachments** - Screenshots, links to docs

User can then:
- ✅ **Approve** - Copy to clipboard for sending
- ✏️ **Edit** - Make changes and regenerate
- 🔍 **Research more** - Dive deeper into specific aspects
- ❌ **Start over** - Try different approach

## Question Type Handlers

### Viability Questions
"Can I build X in Glide?"

**Response format:**
```
Hi [name],

Thanks for reaching out! Yes, you can build [X] in Glide, though there are
a few considerations to keep in mind.

What works well in Glide:
- [Feature 1] - [explanation]
- [Feature 2] - [explanation]

Potential limitations:
- [Limitation 1] - [workaround if available]

For a project like yours, I'd recommend [specific advice].

[Link to relevant doc/template]

Let me know if you'd like help getting started!
```

### Feature Questions
"Does Glide support X?"

**Response format:**
```
Hi [name],

Yes! Glide supports [feature]. Here's how it works:

[Clear explanation with example]

You can find this in [location in Glide UI].

[Link to documentation]

Let me know if you need help setting this up!
```

### How-To Questions
"How do I do X?"

**Response format:**
```
Hi [name],

Great question! Here's how to [do X]:

1. [Step 1]
2. [Step 2]
3. [Step 3]

[Screenshot or link to doc with visuals]

Pro tip: [Helpful additional context]

Let me know if you run into any issues!
```

### Planning Questions
"How should I structure X?"

**Response format:**
```
Hi [name],

Here's how I'd approach structuring [X] in Glide:

Data Model:
- [Table 1]: [columns]
- [Table 2]: [columns]
- Relationships: [how they connect]

Screens:
- [Screen 1]: [purpose, components]
- [Screen 2]: [purpose, components]

[Specific recommendations based on use case]

Would you like me to elaborate on any of these areas?
```

### Troubleshooting Questions
"Why isn't X working?"

**Response format (Keep under 200 words):**
```
Hi [name],

[Personal greeting if appropriate]

The short answer is [direct explanation of limitation/issue].

As an alternative, you could consider:
1. [Approach 1 with concrete action] - [one-line outcome]
2. [Approach 2, potentially challenging the original approach]

[Optional: Very brief why, only if it adds value]

Let me know how you get on and if there's anything else I can help with.

Best,
[name]
```

**Key principles:**
- Start with "the short answer is..." to get to the point
- Offer 2 alternatives max
- Consider suggesting a different approach entirely
- Add personal warmth (greetings, company updates)
- Skip lengthy explanations unless critical
- Offer follow-up

## Browser Automation (Mode 2)

### Gmail Integration

**Navigate and authenticate:**
```
1. Navigate to https://mail.google.com
2. Take snapshot
3. If not logged in → Ask user to log in
4. Wait for inbox to load
```

**Find Glide-related emails:**
```
1. Look for label/folder: "Glide Support" or "Unread"
2. Search for keywords: "glide", "app", "table", "screen"
3. Filter by unread status
4. Select most recent relevant email
```

**Extract email content:**
```
1. Click on email to open
2. Capture snapshot
3. Extract:
   - Sender name and email
   - Subject line
   - Body text
   - Any attachments or screenshots mentioned
4. Take screenshot of question if it includes UI references
```

### Outlook Integration

**Navigate and authenticate:**
```
1. Navigate to https://outlook.office.com
2. Take snapshot
3. If not logged in → Ask user to log in
4. Wait for inbox to load
```

**Same extraction process as Gmail**

## Knowledge Base References

This command reuses the same knowledge sources as `/ask`:

**Primary Sources:**
- All skill files in `glide/skills/`
- All agent procedures in `glide/agents/*/procedures/`

**Search Strategy:**
1. Start with most relevant skill file
2. Use web search if internal docs insufficient
3. Prefer official Glide docs over community solutions
4. Always verify answers against current Glide capabilities

## Example Workflows

### Example 1: Quick Paste Response

**User runs:** `/email-success`

**System:**
> Please paste the email content below:

**User pastes:**
```
From: john@example.com
Subject: Question about Rollup columns

Hi,

I'm trying to sum up sales amounts from a related table but can't figure
out how to do it in Glide. Can you help?

Thanks,
John
```

**System:**
1. Reads `glide/skills/computed-columns/SKILL.md`
2. Finds Rollup column section
3. Generates draft:

```
Subject: Re: Question about Rollup columns

Hi John,

Absolutely! You'll want to use a Rollup column for this. Here's how:

1. In your main table (where you want the sum), add a new column
2. Choose "Rollup" as the column type
3. Select the relation that connects to your sales table
4. Choose "Sum" as the aggregation type
5. Select the amount column you want to sum

The rollup will automatically calculate the total of all related sales.

Here's a doc with screenshots: https://glideapps.com/docs/rollup-columns

Let me know if you need any clarification!

Best,
[Your name]
```

**User can:**
- ✅ Copy and send
- ✏️ Request edits
- 🔍 Get more details about rollups

### Example 2: Browser-Based Batch Processing

**User runs:** `/email-success --inbox gmail`

**System:**
1. Opens Gmail in browser
2. Finds 3 unread Glide-related emails
3. Processes each one:
   - Email 1: Feature question → Draft response
   - Email 2: How-to question → Draft with steps
   - Email 3: Viability question → Draft with assessment

4. Presents all 3 drafts for review
5. User can edit, approve, or request changes
6. Optionally mark emails as read or move to folder

## Advanced Features

### Attach Screenshots

When answering "where is X" questions:
1. Open Glide in browser
2. Navigate to relevant location
3. Take screenshot
4. Suggest attaching to email
5. Save with descriptive filename

### Template Suggestions

For complex questions, suggest relevant Glide templates:
1. Search template store
2. Find matching templates
3. Include template links in response
4. Explain how template addresses their need

### Follow-Up Detection

Detect when email is a follow-up:
1. Check for "Re:" in subject
2. Look for conversation context
3. Reference previous advice
4. Build on prior explanation

### Confidence Scoring

Rate answer confidence:
- 🟢 **High (90-100%)**: Found in internal docs, verified feature
- 🟡 **Medium (70-89%)**: Found via web search, likely current
- 🔴 **Low (<70%)**: Limited info, needs verification

Show confidence to user:
```
Draft ready! Confidence: 🟢 High

This answer is based on:
- Internal skill docs: computed-columns/SKILL.md
- Glide official docs: rollup-columns
```

## Configuration

### Email Signature

Create a file: `glide/config/email-signature.md`

```
Best,
[Your Name]
[Your Title]
[Company]
[Support Email]
```

### Auto-Response Templates

Create templates in: `glide/config/email-templates/`

- `viability-yes.md` - When feature is definitely possible
- `viability-no.md` - When feature isn't suitable
- `feature-available.md` - Feature exists
- `feature-workaround.md` - Feature needs workaround
- `how-to-simple.md` - Simple step-by-step
- `how-to-complex.md` - Complex multi-step process

## Future Enhancements

### Email Hook Integration

1. Set up email forwarding address
2. Parse incoming emails automatically
3. Generate responses
4. Send via API
5. Track response history

### Learning from Responses

1. Save drafted responses
2. Track which answers were edited
3. Learn common patterns
4. Improve future drafts

### CRM Integration

1. Link to customer records
2. Check previous support history
3. Personalize responses based on customer
4. Track resolution time

## Error Handling

### Email Parsing Errors

If email format is unclear:
```
I had trouble parsing that email. Could you confirm:
- Sender name: [extracted or "unknown"]
- Main question: [extracted or "please clarify"]

Or paste just the question part, and I'll draft a response.
```

### Research Failures

If answer not found:
```
I couldn't find a definitive answer in our docs.

What I found:
- [Partial information]
- [Related features]

Recommendation:
- [Ask user to clarify]
- [Suggest alternative approach]
- [Escalate to senior support if needed]

Would you like me to search more broadly or suggest an alternative?
```

### Browser Issues

If browser automation fails:
```
Browser connection issue. Would you like to:
1. Switch to paste mode (paste email content)
2. Retry browser connection
3. Try different email provider
```

## Tips for Best Results

**For Users:**
1. ✅ Use clear email labels/folders for Glide questions
2. ✅ Include relevant screenshots in original email
3. ✅ Review drafts before sending (always!)
4. ✅ Add personal touches to make it your voice
5. ❌ Don't auto-send without review

**For Responses:**
1. ✅ Keep it concise - customers appreciate brevity (under 200 words)
2. ✅ Limit alternatives to 2 max - avoid decision paralysis
3. ✅ Consider paradigm shifts - better approaches vs workarounds
4. ✅ Add personal warmth - greetings, company updates
5. ✅ Offer follow-up - show you're available
6. ❌ Don't overwhelm - start simple, elaborate if asked
7. ❌ Don't overload with links - only essential documentation

## Real-World Example: What Great Responses Look Like

### The Question

Customer trying to filter a list based on URL parameters, but can't filter on an If-Then-Else computed column in Big Tables (SQL).

### ❌ Initial Draft (Too Long, Too Complex)

**Problems:**
- 300+ words (too long)
- 3+ alternatives (decision paralysis)
- Heavy on technical detail upfront
- Multiple community links (overwhelming)
- Focuses on workarounds, not better approaches

**Example excerpt:**
```
You've hit a known limitation... Here are a few workarounds:

Option 1: Use a Query Column Instead
[Long explanation with 4 steps]

Option 2: Move the Logic to a Basic Column
[Another long explanation]

Option 3: Use Relations + Single Value
[Yet another approach]

[Then more detail about why this happens...]
```

### ✅ Refined Response (Effective)

**What works:**
- ~150 words (concise)
- 2 clear alternatives
- Suggests reconsidering the approach (paradigm shift)
- Personal greeting with company update
- Actionable and consultative

**Actual response:**
```
Hi Adam,

I hope you are well and you had a fantastic Christmas and New Year break.

Apologies for the delay in getting back to you, we've had a particularly
busy start to the New Year over here at Glide (lots of exciting things
in the works!)

In response to your question on the URL parameter filtering, the short
answer is that it will not be possible to filter on your SQL table using
the computed if-then-else column. As an alternative, you could consider
having a filter button to trigger a workflow to stamp the values from the
URL parameters to some basic columns within the users table. This would
allow you to query your sql table accordingly and you could have that
query as the source of your collection on that page. Another option would
be, rather than use URL parameter filtering, to consider using choice
components to allow you to show a set of filter options and then writing
the choices (filters) to the user tables to be used in your query.

Let me know how you get on and if there is anything else I can help with.

Best regards,
Stephen
```

### Key Takeaways

1. **Length matters** - Half the length = 2x the impact
2. **Limit choices** - 2 alternatives, not 5
3. **Challenge the premise** - "Rather than use URL parameter filtering, consider choice components"
4. **Personal > Professional** - Warm greeting + company update > corporate tone
5. **Action over explanation** - What to do > why it doesn't work

## Command Workflow Summary

```
START
  │
  ├─ Mode 1: Paste ──────────┐
  ├─ Mode 2: Browser ────────┤
  └─ Mode 3: Hook (future) ──┤
                             │
                             ▼
                    Extract Question
                             │
                             ▼
                    Identify Question Type
                             │
                             ▼
                    Research Answer
                      (internal docs,
                       web search)
                             │
                             ▼
                    Draft Email Response
                             │
                             ▼
                    Present to User
                      (with confidence,
                       sources, options)
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
          Approve         Edit          Research More
              │              │              │
              ▼              ▼              ▼
      Copy to Clipboard  Regenerate    Deep Dive
              │         (loop back)    (loop back)
              ▼
            DONE
```

## Before Running This Command

Make sure you have:
1. ✅ Access to internal Glide documentation (skills, procedures)
2. ✅ Browser automation set up (for Mode 2)
3. ✅ Email signature configured (optional)
4. ✅ Web search capability enabled

## Implementation Notes

This command is designed to:
- Start simple (paste mode) and grow to full automation
- Maintain human oversight (always review before sending)
- Learn from the `/ask` command's research patterns
- Be extensible (add new question types, templates, integrations)
- Prioritize quality over speed (better to ask than guess)
- **Learn from real examples** - See `email-success-examples.md` for before/after comparisons

---

**Remember:** You're representing the company. Always be helpful, accurate, and professional. When in doubt, ask the user to review or suggest alternatives rather than sending potentially incorrect information.
