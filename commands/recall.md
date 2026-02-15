# Recall Learnings

Search the knowledge base for information about a topic using the knowledge MCP server. Do ALL of the following:

## 1. Determine Search Strategy

Use the topic/keyword provided by the user. If none provided, infer from the current conversation context (e.g., if debugging nRF54L15 flashing, search for "nrf54l15 flashing").

Choose the best retrieval mode:

| Situation | Tool to Use |
|-----------|-------------|
| General topic search | `knowledge.search(query, tags?, chips?, category?)` |
| "What do I need to know for these files?" | `knowledge.for_context(files, board?)` |
| "Everything about this chip" | `knowledge.for_chip(chip)` |
| "Everything about this board" | `knowledge.for_board(board)` |
| "What's new since I was last here?" | `knowledge.recent(days?)` |
| "What might be outdated?" | `knowledge.stale(days?)` |
| "What tags/topics exist?" | `knowledge.list_tags(prefix?)` |

## 2. Search

Run the appropriate query. If the first search returns no results, try broader terms or use `list_tags()` to discover the right vocabulary.

## 3. Present Results

- If **1-5 matches**: Present each item with title, severity, category, and body.
- If **6-15 matches**: Show titles and severity for all. Ask the user which to read in full.
- If **>15 matches**: Summarize the count by category/severity and suggest narrowing with tags, chips, or category filters.
- If **0 matches**: Say so and suggest related tags from `list_tags()` or broader search terms.
