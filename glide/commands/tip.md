---
name: tip
description: Get unstuck by providing expert tips that the plugin learns from and adds to its skills
---

# Tip Command - Self-Learning from Expert Feedback

This command allows you (the expert Glide app builder) to provide tips when the plugin gets stuck. The plugin will attempt to follow your tip, and if successful, will update its own skills with what it learned.

## Workflow

### Step 1: Capture Current Context

Before asking for the tip, capture the current state:
- What task was being attempted?
- What went wrong or got stuck?
- Which agent was working on it?
- What was the last action taken?

Store this context for later analysis.

### Step 2: Get Expert Tip

Use AskUserQuestion to get the user's tip:
- Question: "What tip or guidance would help solve this issue?"
- This will allow "Other" input where they can provide detailed instructions

The tip might include:
- How to navigate to a specific UI element
- A better approach to accomplish the task
- Technical details about Glide's behavior
- Workarounds for known issues
- Best practices they've discovered

### Step 3: Attempt to Execute the Tip

Based on the tip provided:
- Parse the instructions
- Determine which agent should execute (builder, data, design, etc.)
- Launch the appropriate agent with the tip as additional context
- Let the agent attempt to follow the tip

Pass to the agent:
- The original task context
- The user's tip/instructions
- Clear directive to follow the tip and report back

### Step 4: Verify Success

After the agent attempts to execute the tip, use AskUserQuestion:
- Question: "Did the tip successfully resolve the issue?"
- Options:
  - "Yes, it worked!" - Proceed to learning extraction
  - "Partially worked" - Capture what worked and what didn't
  - "No, still stuck" - Store the tip for future reference but don't update skills yet

### Step 5: Extract Learnings (If Successful)

If the tip was successful (fully or partially), launch the `skill-learner` agent with:
- **Original context**: What was stuck
- **The tip provided**: User's instructions
- **What was done**: Actions taken by the agent
- **Result**: What worked

The skill-learner agent will:
1. Analyze what was learned
2. Determine which skill(s) should be updated
3. Identify what specific knowledge should be added
4. Update the relevant skill files

### Step 6: Confirm Updates

After the skill-learner agent updates skills:
- Show the user which skills were updated
- Briefly summarize what was added
- Ask if they want to review the changes

## Implementation Notes

### Context Capture

Create a context object with:
```javascript
{
  "timestamp": "2025-01-01T12:00:00Z",
  "task": "Creating a computed column for total price",
  "agent": "builder",
  "stuckPoint": "Couldn't find the 'Add column' button",
  "lastAction": "Clicked on Data tab, took snapshot",
  "errorMessage": "Element not found: Add column button"
}
```

### Tip Execution

- Don't try to execute the tip yourself - launch the appropriate agent
- Give the agent the full context and tip
- Let the agent work autonomously
- The agent should report back on what it did

### Learning Storage

The skill-learner agent should:
- Read existing skills to avoid duplicates
- Add learnings in the appropriate section
- Use clear, actionable language
- Include specific examples when relevant
- Format consistently with existing skill content

### Skill Update Strategy

The learner should prioritize:
1. **Specificity**: Add concrete details, not vague advice
2. **Context**: Include when/why this approach works
3. **Examples**: Show the pattern that worked
4. **Integration**: Fit naturally into existing skill structure

## Example Usage

```
User runs: /tip
Claude: "What tip or guidance would help solve this issue?"
User: "To add a column, you need to click the '+' icon in the column header, not look for an 'Add column' button. The icon is subtle and easy to miss."

Claude: [Launches builder agent with this tip]
Builder agent: [Successfully adds column using the '+' icon]

Claude: "Did the tip successfully resolve the issue?"
User: "Yes, it worked!"

Claude: [Launches skill-learner agent]
Skill-learner: [Updates the 'layout' skill with a new section about adding columns]

Claude: "I've updated the 'layout' skill with the following learning:
- Added detailed guidance on using the '+' icon in column headers to add columns
- Noted that there's no separate 'Add column' button
- Included this in the 'Adding Columns' section

Would you like to review the changes?"
```

## Benefits

1. **Self-improving**: Plugin gets smarter with each tip
2. **Expert knowledge capture**: Your experience becomes part of the plugin
3. **Reduced repetition**: Same mistakes won't happen again
4. **Growing expertise**: Plugin learns nuances of Glide over time
5. **Customized to your workflow**: Learns patterns specific to how you build

## Future Enhancements

Consider adding:
- A learning log that tracks all tips and updates
- Ability to review and rollback skill updates
- Export learnings to share with others
- Pattern detection across multiple tips
- Suggest proactive tips based on context
