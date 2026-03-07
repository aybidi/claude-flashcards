---
description: Review due flashcards with spaced repetition
allowed-tools: Bash(python3:*)
---

Start a flashcard review session.

Use the session-flashcards skill for review presentation guidelines.

Load due cards: !`python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards list-due`

If no cards are due, tell me and show stats from: `python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards stats`

If cards are due, walk me through them one at a time:
1. Show the question only (with card number, category, and source repo)
2. Wait for me to say "show" or similar before revealing the answer
3. After revealing, ask me to rate: (1) Again (2) Hard (3) Good (4) Easy
4. Record my rating with: `python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards rate <card_id> <quality>`
5. If I rate "Again", briefly re-explain the concept to reinforce it
6. Move to the next card

After all cards are done, show a brief summary of the review session.
