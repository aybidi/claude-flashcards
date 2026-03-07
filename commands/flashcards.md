---
description: Generate flashcards from this session
allowed-tools: Bash(python3:*), Bash(echo:*), Bash(basename:*), Bash(git:*)
---

Analyze our conversation and generate spaced-repetition flashcards capturing the key things I learned in this session.

Use the session-flashcards skill for card quality guidelines, categories, and format.

Determine the source repo: !`basename $(git rev-parse --show-toplevel 2>/dev/null) 2>/dev/null || echo "unknown"`

Steps:
1. Review the full conversation for learning moments
2. Generate 3-8 high-quality cards (prefer fewer, better cards)
3. Present the cards in a numbered list so I can see them
4. Save by piping the JSON array to: `echo '<json_array>' | python3 ${CLAUDE_PLUGIN_ROOT}/scripts/flashcards add`
5. Show a brief summary of what was saved

If I ask to remove or edit any cards, do that before saving.
