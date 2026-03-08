---
description: Generate flashcards from this session
allowed-tools: Bash(python3:*), Bash(echo:*), Bash(basename:*), Bash(git:*)
---

Analyze our conversation and generate spaced-repetition flashcards capturing the key things I learned in this session.

Use the session-flashcards skill for card quality guidelines, categories, and JSON format.

Determine the source repo: !`basename $(git rev-parse --show-toplevel 2>/dev/null) 2>/dev/null || echo "unknown"`

Check existing topics for consistency: !`python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards list-due 2>/dev/null | python3 -c "import sys,json; cards=json.load(sys.stdin).get('due',[]); topics=sorted(set(c.get('topic','') for c in cards if c.get('topic'))); print('Existing topics: '+', '.join(topics) if topics else 'No existing topics')" 2>/dev/null || echo "No existing cards"`

Steps:
1. Review the full conversation for learning moments
2. Generate 3-8 high-quality cards (prefer fewer, better cards)
3. For each card provide ONLY: question, answer, category, source_repo, and optionally topic
4. Do NOT include id, created, sm2, theme, tags, or any other fields
5. Reuse existing topic names when a card fits an existing topic
6. Present the cards in a numbered list so I can review them
7. If I ask to remove or edit any cards, do that before saving
8. Save by piping the JSON array to the script (NEVER edit cards.json directly):
   `echo '<json_array>' | python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards add`
9. Show a brief summary: count, categories, topics
