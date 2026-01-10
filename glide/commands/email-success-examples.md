# Email Success Examples

Real-world examples of customer support emails showing what works and what doesn't.

## Example 1: URL Parameter Filtering with Big Tables

### Customer Question

```
Hey Stephen!

I hope you had a happy Christmas! Just wanted to check in with you to see
if you can help me get unstuck on this last feature. I'm trying to
essentially expose a list of items based on a URL parameter (projectId)
and I cant seem to make it work.

I did find this walkthrough on a hack to make it work
https://www.loom.com/share/cb256cdbba8c4cb4aa16cef983d0fb1d

I managed to get the fields setup and I've tested that they work BUUUT...
Apparently I can not FILTER on a computed IF-THEN-ELSE column in my setup
(perhaps a limitation because I'm using sql or just a new limitation after
the video)

I'm doing this in my new app 'Contents Reports' on the only screen. If you
want to take a look you're still on my team.

Thanks in advance!
```

### ❌ Initial AI Draft (Too Long)

**Word count:** ~300 words
**Alternatives offered:** 3
**Links included:** 5 community/doc links
**Tone:** Technical, comprehensive

```
Subject: Re: URL Parameter Filtering - Alternative Approaches

Hi there!

Thanks for reaching out, and happy new year!

You've hit a known limitation with Glide Big Tables - filtering directly
on computed columns (like If-Then-Else) isn't currently supported when
working with SQL-backed Big Tables. This is exactly what you're
experiencing.

Here are a few workarounds you can try:

## Option 1: Use a Query Column Instead

Rather than filtering the collection directly on the If-Then-Else column,
try using a Query column:

1. In your data table, add a Query computed column
2. Set the source to your table
3. Add a condition that matches your URL parameter directly to the original
   column (not the If-Then-Else column)
4. Then bind your collection to this Query column instead

Query columns can filter on basic column types and are fast with Big Tables.

## Option 2: Move the Logic to a Basic Column

If possible, populate the matching logic into a basic Text or Number column
rather than computing it with If-Then-Else:

- Use a workflow or integration to write the computed value to a basic
  column when data is added/updated
- Then filter on that basic column instead

Basic columns support filtering, whereas computed columns have limitations
in Big Tables.

## Option 3: Use Relations + Single Value

If your filtering is based on matching IDs:

1. Create a Relation column that matches your URL parameter to the relevant
   table
2. Use a Single Value column to pull through a field from that relation
3. Use visibility conditions or collection filters based on whether the
   relation exists

This pattern often works well for URL parameter-based filtering.

## Why This Happens

Big Tables are optimized for performance with large datasets, but this means
some computed column operations (like If-Then-Else) aren't available for
filtering. The workaround in that Loom video likely worked with Glide Tables
(non-SQL), which have different limitations.

## Next Steps

I'd be happy to take a look at your "Contents Reports" app to suggest the
best approach for your specific data structure. Could you share:
- What column you're trying to filter on
- What the If-Then-Else logic is checking
- What the URL parameter represents

That way I can recommend the most elegant solution for your setup!

Let me know if you'd like me to hop into the app and help you implement one
of these approaches.

Best,
[Your name]
```

**Problems with this draft:**
- ❌ Too long (300+ words)
- ❌ 3 options = decision paralysis
- ❌ Heavy technical detail upfront
- ❌ 5+ links overwhelming
- ❌ Focuses on working around the limitation
- ❌ Doesn't challenge the approach itself

### ✅ Actual Response (Effective)

**Word count:** ~150 words
**Alternatives offered:** 2
**Links included:** 0
**Tone:** Personal, consultative, action-oriented

```
Hi Adam,

I hope you are well and you had a fantastic Christmas and New Year break.

Apologies for the delay in getting back to you, we've had a particularly
busy start to the New Year over here at Glide (lots of exciting things in
the works!)

In response to your question on the URL parameter filtering, the short
answer is that it will not be possible to filter on your SQL table using
the computed if-then-else column. As an alternative, you could consider
having a filter button to trigger a workflow to stamp the values from the
URL parameters to some basic columns within the users table. This would
allow you to query your sql table accordingly and you could have that query
as the source of your collection on that page. Another option would be,
rather than use URL parameter filtering, to consider using choice components
to allow you to show a set of filter options and then writing the choices
(filters) to the user tables to be used in your query.

Let me know how you get on and if there is anything else I can help with.

Best regards,
Stephen
```

**Why this works:**
- ✅ ~150 words (concise)
- ✅ 2 clear alternatives
- ✅ Second option challenges the premise (choice components vs URL params)
- ✅ Personal greeting + company update
- ✅ "The short answer is..." gets to the point
- ✅ No link overload
- ✅ Actionable and consultative
- ✅ Offers follow-up without being pushy

### Key Learnings

1. **Half the length = 2x the impact**
   - 150 words > 300 words
   - Get to the point faster

2. **Limit alternatives to 2**
   - 3+ options = decision paralysis
   - 2 concrete paths = actionable

3. **Challenge the approach**
   - Don't just solve the problem they asked about
   - Consider if there's a better way entirely
   - "Rather than use URL parameter filtering, consider choice components"

4. **Personal > Corporate**
   - Holiday greetings work
   - Company updates add warmth
   - "Lots of exciting things in the works!" > generic response

5. **Action > Explanation**
   - What to do > why it doesn't work
   - Skip the deep technical "why" unless critical
   - Offer to dive deeper if needed

6. **Minimal links**
   - 0-1 links max for simple questions
   - Only include if absolutely necessary
   - Community links can overwhelm

7. **Start with "The short answer is..."**
   - Signals you're getting to the point
   - Customer knows the main info is coming
   - Can elaborate after if needed

---

## Template for Future Responses

```
Hi [name],

[Personal greeting - holidays, company updates, warmth]

The short answer is [direct answer to their question/limitation].

As an alternative, you could consider:
1. [Concrete approach with action steps] - [one-line outcome]
2. [Different paradigm that might be better] - [why it's worth considering]

[Optional: Very brief technical context, only if valuable]

Let me know how you get on and if there is anything else I can help with.

Best regards,
[Your name]
```

**Checklist:**
- [ ] Under 200 words
- [ ] Personal greeting included
- [ ] 2 alternatives max
- [ ] At least one challenges the original approach
- [ ] Action-oriented language
- [ ] Minimal/no links
- [ ] Offers follow-up
- [ ] Consultative tone
