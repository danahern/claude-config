# Deliberate

Orchestrate a structured multi-agent debate to analyze a complex decision or problem. Three agents (advocate, critic, synthesizer) debate via a shared log file on disk, producing a well-reasoned recommendation.

**Usage:** `/deliberate <question>` — where `<question>` is the precise question to debate.

**When to use:**
- Architecture decisions with significant reversibility cost
- Debugging approaches where you've been stuck 2+ attempts
- Design decisions with multiple viable approaches
- Pre-commit review of complex, cross-cutting changes

**When NOT to use:**
- Straightforward or well-established answers
- Purely mechanical tasks
- Factual questions (not judgment-based)
- Time pressure outweighs deliberation value

## Step 1: Frame the Question

Parse the user's input to extract the question. If the question is vague, ask the user to make it more precise before proceeding.

Create the debate log file:

```bash
mkdir -p .claude/debates
```

Generate a filename: `.claude/debates/<YYYY-MM-DD>-<slug>.md` where `<slug>` is a short kebab-case summary of the question (max 5 words).

Write the initial debate log with this template:

```markdown
# Deliberation: <slug>

## Meta
- **Question**: <the precise question>
- **Started**: <ISO timestamp>
- **Status**: in-progress
- **Domain experts consulted**: none
- **Outcome**: pending

## Context
<to be filled in Step 2>
```

Tell the user: "Starting deliberation on: **<question>**"

## Step 2: Gather Context

Build the Context section of the debate log. Do ALL of the following that apply:

1. **Search knowledge** — Run `knowledge.search()` and/or `knowledge.for_context()` for relevant prior learnings. Add key findings to Context.
2. **Read relevant files** — If the question references specific files, code, or systems, read them and summarize relevant parts in Context.
3. **Consult domain experts** (optional) — If the question clearly falls within an existing agent's expertise, invoke that agent first for a technical brief. Add their output under a `## Domain Expert Input` section. Update the "Domain experts consulted" field in Meta. Good candidates:
   - `kernel-reviewer` for kernel/driver questions
   - `build-toolchain-expert` for build system questions
   - `platform-engineer` for board bring-up questions
   - `embedded-specialist` for firmware architecture questions

Write the gathered context into the debate log file.

## Step 3: Round 1 — Propose

Invoke the `advocate` agent with this prompt:

> You are participating in a structured deliberation. Read the debate log at `<debate-log-path>` to understand the question and context. This is Round 1 — write your initial Proposal.
>
> After writing your proposal, append it to the debate log under a `## Round 1` heading. Use the exact format specified in your system prompt.
>
> Write your proposal section directly into the debate log file at `<debate-log-path>`.

Wait for the advocate to complete.

## Step 4: Round 1 — Critique

Invoke the `critic` agent with this prompt:

> You are participating in a structured deliberation. Read the ENTIRE debate log at `<debate-log-path>`, including the proposal that was just written. Write your Round 1 Critique.
>
> Steelman the proposal first, then provide severity-rated critique. Append your critique to the debate log under the existing `## Round 1` heading.
>
> Write your critique section directly into the debate log file at `<debate-log-path>`.

Wait for the critic to complete.

## Step 5: Evaluate — Round 2 Needed?

Read the critic's output from the debate log. Determine if Round 2 is needed:

**Skip Round 2 if:**
- All issues are rated "Could be better" (no "Wrong" or "Risky" items)
- The critique is purely about style or minor improvements
- The advocate already addressed the key concerns proactively

**Run Round 2 if:**
- Any issue is rated "Wrong" — factual/logical errors need response
- Multiple "Risky" items — the proposal needs meaningful revision
- The critic identified a viable alternative that wasn't considered
- Key assumptions were questioned and need verification

If skipping, tell the user: "Critique was minor — skipping Round 2, moving to synthesis."

If running Round 2, tell the user: "Substantive critique raised — running Round 2."

### Round 2: Revised Proposal

Invoke the `advocate` agent:

> You are participating in a structured deliberation. Read the ENTIRE debate log at `<debate-log-path>`, including the Round 1 critique. This is Round 2 — write your Revised Proposal addressing the critique.
>
> You MUST explicitly address every "Wrong" and "Risky" issue from the critique. Append your revised proposal under a `## Round 2` heading.
>
> Write directly into the debate log file at `<debate-log-path>`.

### Round 2: Critique

Invoke the `critic` agent:

> You are participating in a structured deliberation. Read the ENTIRE debate log at `<debate-log-path>`, including the Round 2 revised proposal. Write your Round 2 Critique.
>
> Acknowledge which issues from Round 1 were resolved. Focus on what remains. Append under the `## Round 2` heading.
>
> Write directly into the debate log file at `<debate-log-path>`.

**Maximum 3 rounds.** If Round 2 critique still has "Wrong" items, you may run Round 3, but no more. Diminishing returns set in fast.

## Step 6: Synthesize

Invoke the `synthesizer` agent:

> You are the synthesizer in a structured deliberation. Read the ENTIRE debate log at `<debate-log-path>` — all rounds, all domain expert input.
>
> Write your Synthesis integrating all perspectives into a clear, actionable recommendation. Append it as a `## Synthesis` section at the end of the debate log.
>
> Write directly into the debate log file at `<debate-log-path>`.

Wait for the synthesizer to complete.

## Step 7: Present and Decide

Read the full debate log, focusing on the Synthesis section.

Present to the user:
1. **The synthesizer's recommendation** — summarized concisely
2. **Key tradeoffs** — what's being gained and given up
3. **Remaining risks** — anything unresolved
4. **Suggested next steps** — from the synthesis

Ask the user for their decision. Once they decide:

1. Write a `## Decision` section to the debate log:
   ```markdown
   ## Decision
   **Decision**: <what was decided>
   **Rationale**: <why, including what was accepted/rejected from the synthesis>
   **Decided by**: user
   **Date**: <ISO date>
   ```

2. Update the Meta section: set **Status** to `complete` and **Outcome** to a one-line summary.

3. If the debate produced valuable learnings, capture them with `knowledge.capture()` tagged with `deliberation`.

Report the debate log path to the user so they can reference it later.
