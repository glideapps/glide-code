# Generate Customer Report Procedure

Translate a completed app-audit into a plain-language, shareable document for the customer who reported the issue.

## Overview

This procedure produces an **additional, optional** artifact. It does **not** replace the technical report from [generate-report.md](generate-report.md) — it re-presents the same findings for a non-technical reader, focused on helping them optimize their app for **speed, complexity, and reliability**.

It performs **no new analysis**. It consumes the analysis object already produced by the audit and reframes it according to [../customer-report-guideline.md](../customer-report-guideline.md).

**Read [../customer-report-guideline.md](../customer-report-guideline.md) first** — it defines the tone, the omit-list, and the jargon→plain-language table this procedure depends on.

## Input

The same combined analysis object consumed by [generate-report.md](generate-report.md):

```javascript
{
  appId, appName, auditDate,
  dataAnalysis:   { issues: [...], tableAnalysis: [...], summary: {...}, scores: {...} },
  layoutAnalysis: { issues: [...], screenAnalysis: [...], scores: {...} }
}
```

Each issue keeps its existing shape: `{ severity, category, type, location, description, details, impact, recommendation }`.

**Plus** one optional input:

```javascript
reportedSymptoms: ["the second tab is slow", "the list is blank until I reopen the app"]
// or free text; or omitted entirely
```

If `reportedSymptoms` is not supplied and a customer report is requested, ask once:
> "Any specific symptoms the customer reported? Otherwise I'll organize the report by Speed, Complexity, and Reliability."

## Output

A standalone markdown document, presented **after** the technical report. Do not overwrite or merge into the technical report.

## Mode Selection

- **`reportedSymptoms` present →** use the **symptom-led** structure.
- **Absent →** use the **theme-led** structure (Speed / Complexity / Reliability).

Both structures are defined in [../customer-report-guideline.md](../customer-report-guideline.md#document-structures).

## Issue → Theme Mapping (theme-led mode)

Group the existing issues into the three customer themes. Map by `issue.type` / `issue.category`:

| Theme | Issue types that belong here |
|-------|------------------------------|
| **Speed** | deep-column-chain, rollup-without-relation, heavy-ai-usage, large-collection / item-count, inline-collection-overload, component-overload, heavy first screen, large images |
| **Complexity** | many relations, nested computed chains, query-columns (could be relations), high computed-column percentage, duplicate/leftover tabs and screens |
| **Reliability** | computed-value-not-ready / "blank until reopen" timing, row-limit (approaching/exceeded), missing access control (no Row Owners), external data source / sync lag |

An issue may legitimately appear under more than one theme (e.g., Query columns affect both Speed and Complexity) — place it where it most helps the customer act, and don't repeat it.

## Symptom → Likely-Cause Mapping (symptom-led mode)

For each reported symptom, find the detected issue(s) that most plausibly explain it. Examples:

| Reported symptom | Likely causes to look for in the findings |
|------------------|-------------------------------------------|
| A tab/screen is slow to load | Heavy first-screen weight; multiple Maps on one screen; collection item count over ~24; expensive computed columns (AI, Query, rollups) shown in a collection on that screen |
| A list/collection is blank until reopen | A filter that compares a **computed value** not ready at first paint; external data source still syncing; an experimental column type feeding the filter |
| App is slow overall | Missing Row Owners (whole dataset synced to every device); external data source; high computed-column load |
| Data looks wrong / out of date | External data source sync lag; rollup operating on a table instead of a relation |

State the connection tentatively ("a likely contributor is…"). **If no detected issue plausibly explains a symptom, say so honestly** ("we didn't find an obvious cause for X in our review; here's what we'd check") rather than forcing a fit.

## Translating Findings

For every issue you include:
1. Rewrite the **description/impact** using the jargon→plain-language table in the guideline.
2. Pull the **fix steps** from [../recommendations.md](../recommendations.md) for that issue type, and **reframe them in plain language** — do not copy phrasing that names internal tools.
3. Apply the tone rules: tentative ("changes to try / should help"), observational ("in our review we observed…"), honest about uncertainty.
4. Pull the **Effort** value for the priority table from the Fix Priority Matrix in [../recommendations.md](../recommendations.md) ("Time to Fix").

## Document Template

```markdown
# {App Name} — Performance Review & Recommendations

{Intro: thank them; note what was reviewed (data, calculations, the screens mentioned);
set expectations that performance is usually several factors and these are changes to try.}

## {Symptom-led: one section per reported symptom}  /  {Theme-led: Speed / Complexity / Reliability}

{Per section: what they're seeing or the theme → likely cause(s) in plain language →
"Changes to try" as numbered steps ordered by expected impact.}

## Broader recommendations

{Architecture items — access control, data source, complexity — kept separate from the quick fixes above.}

## Suggested order of work

| # | Change | Effort | What it targets |
|---|--------|--------|-----------------|
| 1 | … | Low | … |

## A few things worth checking on your side

- {open questions the customer can confirm}

{Offer to help / pair on the quick fixes first.}
```

## Guardrails

- **Omit** everything on the guideline's [What to Omit](../customer-report-guideline.md#what-to-omit) list: 0–100 scores, severity emoji/labels, internal-tool names (Dev Tools, Performance Analysis tool, API token, support session), internal process notes, raw internal IDs.
- **Tone:** never "this will fix it" / "root cause." Always "should help" / "likely" / "worth trying."
- **No over-promising:** detected issues correlate with symptoms but don't prove causation — keep it tentative and suggest testing one change at a time.
- **Read-only:** this is a pure text transform over existing audit data. It introduces **no** browser actions and **no** API calls — do not add any. The audit's read-only guarantee is preserved.

## Output Delivery

Return the customer report as a separate markdown string, presented after the technical report (and, if the operator wants, saved to a file for sending). Example wrapper:

```javascript
return {
  customerReport: generatedMarkdown,
  mode: reportedSymptoms ? "symptom-led" : "theme-led"
};
```

## References

- [../customer-report-guideline.md](../customer-report-guideline.md) — tone, omit-list, translation table, structures, worked example.
- [../recommendations.md](../recommendations.md) — source of fix steps and the Effort/priority matrix.
- [../analysis-patterns.md](../analysis-patterns.md) — source of the plain-language "why."
- [generate-report.md](generate-report.md) — the technical report this one accompanies.
