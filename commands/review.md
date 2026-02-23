# Code Review Against Knowledge Base

Review staged (or unstaged) changes against the knowledge store for known anti-pattern violations. Do ALL of the following:

## 1. Get the Diff

Run `git diff --staged` to get the staged diff. If nothing is staged (empty output), fall back to `git diff` for unstaged changes. If both are empty, tell the user there are no changes to review and stop.

Note which mode you're using (staged vs unstaged) so you can report it.

## 2. Extract Changed File Paths

Parse the diff output for changed file paths (the `+++ b/...` lines). Collect the unique list of relative paths.

## 3. Load Relevant Knowledge

Call `knowledge.for_context(files=[...changed file paths...])` to retrieve knowledge items that match the changed files.

If no knowledge items are returned, report cleanly and stop:
```
Code Review: N files changed, no matching knowledge items
No domain-specific checks apply to these files.
```

## 4. Review Diff Against Knowledge

For each knowledge item returned, check whether the diff introduces a violation of that item:

- Read the knowledge item's title, body, and severity
- Scan the added lines (lines starting with `+`) in the diff for the anti-pattern described
- Consider the surrounding context — a pattern may be fine in one context but wrong in another
- Only flag lines that are **newly added or modified** — don't flag pre-existing code

Be precise. Only flag clear violations where you have high confidence. If uncertain, note it as a suggestion rather than a finding.

## 5. Report Findings

Present a compact report:

```
Code Review: N files changed, M findings (staged|unstaged)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚠ SEVERITY: "Knowledge Item Title"
  file/path.c:NN — description of what the diff does wrong
  → Recommended fix

✓ No issues for N other knowledge items checked
```

Severity markers:
- `🔴 CRITICAL` — will cause bugs, data loss, or crashes
- `⚠ IMPORTANT` — likely to cause problems
- `💡 INFO` — suggestion based on past experience

If no violations found:
```
Code Review: N files changed, 0 findings (staged|unstaged)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Checked against M knowledge items — no violations detected.
```

## Rules

- **Never fabricate issues.** If the knowledge items don't describe a pattern violated by the diff, report zero findings.
- **Be specific.** Quote the offending line from the diff. Reference the knowledge item by title.
- **Don't be a linter.** Only flag domain knowledge violations, not style or general code quality issues.
- **Don't read files beyond the diff** unless you need surrounding context to determine if a pattern is actually violated.
