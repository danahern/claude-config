# Recall Learnings

Search the learnings corpus for knowledge about a topic. Do ALL of the following:

## 1. Determine Search Terms

Use the topic/keyword provided by the user. If none provided, infer from the current conversation context (e.g., if debugging nRF54L15 flashing, search for `nrf54l15` and `flashing`).

## 2. Search Learnings

Search `learnings/` using Grep with multiple strategies:

1. **By tag**: `tags:.*KEYWORD` in `learnings/**/*.md`
2. **By title**: `title:.*KEYWORD` in `learnings/**/*.md`
3. **By body content**: `KEYWORD` in `learnings/**/*.md`

Use case-insensitive search. Combine results, deduplicating files that match multiple strategies.

## 3. Present Results

- If **1-5 matches**: Read each file and present the full learning with title, date, and tags.
- If **6-15 matches**: Show titles and tags for all matches. Ask the user which ones to read in full.
- If **>15 matches**: Summarize the count and suggest narrowing the search with more specific terms or tag combinations.
- If **0 matches**: Say so and suggest related tags or broader search terms.

## 4. Check Rules Files

Also check `.claude/rules/*.md` for relevant curated summaries — these may have additional context beyond individual learning files.
