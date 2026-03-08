---
description: Review due flashcards with active recall and spaced repetition
allowed-tools: Bash(python3:*)
---

Start a flashcard review session using the session-flashcards skill for guidelines.

Load due cards: !`python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards list-due`

If no cards are due, tell me and show stats from: `python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards stats`

If cards are due, first show a topic/repo picker:
1. Group the due cards by topic (if present) or source_repo as fallback
2. Show a numbered list of groups with due card counts, plus an "All" option
3. Wait for me to pick before starting

Example:
```
28 cards due for review:

1. llama.cpp (18 cards)
2. Claude Agent SDK (3 cards)
3. Claude API (5 cards)
a. All (28 cards)

Pick a group:
```

After I pick, review only cards in that group using active recall:
1. Show the question (with card number, category, topic, and source repo)
2. Ask me to TYPE my answer (not just flip -- I should try to recall from memory)
3. After I answer, reveal the correct answer
4. Evaluate my response: compare what I said to the correct answer
5. Suggest a rating based on accuracy:
   - (1) Again = completely wrong or blank
   - (2) Hard = partially correct, missed key details
   - (3) Good = correct with minor gaps
   - (4) Easy = perfect, instant recall
6. Let me accept or override your suggestion
7. Record my rating with the script (NEVER edit cards.json directly):
   `python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards rate <card_id> <quality>`
   where quality is 0=Again, 1=Hard, 2=Good, 3=Easy
8. Show the next review date from the script's JSON output
9. Move to the next card

After all cards in the group, show a summary: cards reviewed, rating breakdown, next review date.
