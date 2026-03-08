# claude-flashcards

A Claude Code plugin that generates spaced-repetition flashcards from your coding sessions.

The problem: you pair with Claude Code across different repos, solve hard problems, learn new things -- and then forget most of it within a week. This plugin captures what you learned and turns it into flashcards that stick.

## How it works

**Generate** -- after a coding session, run `/claude-flashcards:generate`. Claude analyzes your conversation, identifies the key things you learned, and creates flashcards.

**Review** -- run `/claude-flashcards:review`. Claude shows your due cards grouped by topic, you pick a group, then walk through them with active recall and spaced repetition (SM-2 algorithm).

Two commands. No external dependencies beyond Python 3.

## Install

Add the marketplace and install the plugin:

```
/plugin marketplace add aybidi/claude-flashcards
/plugin install claude-flashcards@claude-flashcards
/reload-plugins
```

For local development/testing:

```bash
git clone https://github.com/aybidi/claude-flashcards.git
claude --plugin-dir ./claude-flashcards
```

**Requirements:** Python 3.6+ (no external dependencies).

## Usage

### Generate cards from a session

After working on something with Claude -- debugging an issue, learning a new API, making architecture decisions -- run:

```
/claude-flashcards:generate
```

Claude reviews the conversation and generates 3-8 flashcards like:

```
Here are 4 cards from this session:

1. [concept] Why does useEffect with an empty dependency array
   run after paint, unlike componentDidMount?
   -> useEffect is asynchronous (runs after paint), while
      componentDidMount is synchronous (runs before paint).
      Use useLayoutEffect when you need to measure DOM layout.

2. [debugging] What causes "ECONNREFUSED" when connecting to
   a Docker container's exposed port?
   -> The service inside the container is binding to 127.0.0.1
      instead of 0.0.0.0. Containers need to listen on all
      interfaces for port mapping to work.

Saved 4 cards (1 concept, 1 debugging, 1 pattern, 1 tool).
```

### Review due cards

```
/claude-flashcards:review
```

Claude shows your due cards grouped by topic or repo:

```
28 cards due for review:

1. llama.cpp (18 cards)
2. Claude Agent SDK (3 cards)
3. Claude API (5 cards)
a. All (28 cards)

Pick a group:
```

After you pick, Claude walks through cards one at a time using active recall -- you type your answer from memory, then Claude reveals the correct answer and evaluates your response:

```
Card 1/3  |  debugging  |  my-api

Q: What causes "ECONNREFUSED" when connecting to a Docker
   container's exposed port?

Type your answer:
```

After you answer:

```
Your answer: something about binding to localhost

Correct answer: The service inside the container is binding to
127.0.0.1 instead of 0.0.0.0. Containers need to listen on all
interfaces for port mapping to work.

You got the right idea but missed the 0.0.0.0 detail.
Suggested rating: (2) Hard

Accept, or pick: (1) Again  (2) Hard  (3) Good  (4) Easy
```

## What makes good flashcards

The plugin is opinionated about card quality:

- **One concept per card** -- no multi-part questions
- **Tests understanding, not recall** -- "Why does X happen?" over "What command does Y?"
- **Grounded in your context** -- references the real problem you solved
- **Skips the obvious** -- no cards for things you already knew or can easily search

Cards are categorized as: `concept`, `pattern`, `debugging`, `tool`, or `architecture`.

## Where cards are stored

Cards live in `~/.claude-flashcards/cards.json`. This is global -- cards from all repos accumulate in one place, tagged with their source repo and optional topic so you can review by group.

## How spaced repetition works

The plugin uses the [SM-2 algorithm](https://en.wikipedia.org/wiki/SuperMemo#Description_of_SM-2_algorithm):

- New cards rated "Good" start at 1 day, "Easy" starts at 4 days
- Subsequent "Good" ratings increase the interval (1 -> 6 -> ~15 -> ...)
- "Again" resets the card to 1 day
- Cards with 21+ day intervals are considered "mature"

The result: you review new/hard cards frequently and easy cards rarely, maximizing retention per minute spent.

## Plugin structure

```
claude-flashcards/
  .claude-plugin/
    plugin.json                    -- plugin metadata
    marketplace.json               -- marketplace catalog
  commands/
    generate.md                    -- /claude-flashcards:generate
    review.md                      -- /claude-flashcards:review
  scripts/flashcards               -- Python data layer (~180 lines)
```

## License

MIT
