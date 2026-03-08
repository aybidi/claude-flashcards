---
description: Review due flashcards with active recall and spaced repetition
allowed-tools: Bash(python3:*)
---

Start a flashcard review session using active recall and spaced repetition.

## Critical Rules

1. **NEVER edit ~/.claude-flashcards/cards.json directly.** Always use the script commands.
2. **ALWAYS use the script to rate cards** -- the script handles SM-2 scheduling and data integrity.

## Loading Cards

Load due cards by running:
`python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards list-due`

If no cards are due, tell me and show stats by running: `python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards stats`

## Topic/Repo Picker

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

## Active Recall Flow

After I pick, review only cards in that group:

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

### Review Presentation

```
Card 1/5  |  category  |  topic  |  source-repo

Q: [question]

Type your answer:
```

After the user answers:

```
Your answer: [user's answer summary]

Correct answer: [stored answer]

[Evaluation: "Spot on!", "Partially right -- you missed X", "Not quite -- the key insight is X"]

Suggested rating: (3) Good
Accept, or pick: (1) Again  (2) Hard  (3) Good  (4) Easy
```

## After All Cards

Show a session summary with card count, rating breakdown, and next review date.

## Error Handling

- **No cards due**: "All caught up! You have N total cards. Next review: [date]."
- **No cards exist**: "No flashcards yet. Run /flashcards after a coding session to generate your first cards."
- **Script not found**: "The flashcards script wasn't found. The plugin may need to be reinstalled."
