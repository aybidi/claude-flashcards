# claude-flashcards

A Claude Code plugin that generates spaced-repetition flashcards from your coding sessions.

The problem: you pair with Claude Code across different repos, solve hard problems, learn new things -- and then forget most of it within a week. This plugin captures what you learned and turns it into flashcards that stick.

## How it works

**Generate** -- after a coding session, run `/flashcards`. Claude analyzes your conversation, identifies the key things you learned, and creates flashcards.

**Review** -- run `/review` and Claude walks you through your due cards with spaced repetition (SM-2 algorithm). Cards you struggle with come back sooner. Cards you know well space out to weeks, then months.

That's it. Two commands.

## Install

Clone this repo into your Claude Code plugins directory:

```bash
git clone https://github.com/abdullahmobeen/claude-flashcards.git ~/.claude/plugins/claude-flashcards
```

Restart Claude Code. You should see `/flashcards` and `/review` in `/help`.

**Requirements:** Python 3.6+ (no external dependencies).

## Usage

### Generate cards from a session

After working on something with Claude -- debugging an issue, learning a new API, making architecture decisions -- run:

```
/flashcards
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

3. [pattern] How do you retry a failed HTTP request with
   exponential backoff in Python?
   -> Use a loop with sleep(base * 2**attempt), capped at a
      max delay. Add jitter (random offset) to prevent
      thundering herd when multiple clients retry together.

4. [tool] How do you find which process is using a specific
   port on macOS?
   -> lsof -i :<port> shows the process. Use -P to show port
      numbers instead of names, and -n to skip DNS resolution.

Saved 4 cards (1 concept, 1 debugging, 1 pattern, 1 tool).
```

### Review due cards

```
/review
```

Claude presents your due cards one at a time:

```
Card 1/3  |  debugging  |  my-api

Q: What causes "ECONNREFUSED" when connecting to a Docker
   container's exposed port?

Say "show" to reveal the answer.
```

You think, then say "show":

```
A: The service inside the container is binding to 127.0.0.1
   instead of 0.0.0.0. Containers need to listen on all
   interfaces for port mapping to work.

How did you do?  (1) Again  (2) Hard  (3) Good  (4) Easy
```

Rate yourself. If you say "Again", Claude re-explains the concept before moving on.

## What makes good flashcards

The plugin is opinionated about card quality:

- **One concept per card** -- no multi-part questions
- **Tests understanding, not recall** -- "Why does X happen?" over "What command does Y?"
- **Grounded in your context** -- references the real problem you solved
- **Skips the obvious** -- no cards for things you already knew or can easily search

Cards are categorized as: `concept`, `pattern`, `debugging`, `tool`, or `architecture`.

## Where cards are stored

Cards live in `~/.claude-flashcards/cards.json`. This is global -- cards from all repos accumulate in one place, tagged with their source repo so you can see where each insight came from.

## How spaced repetition works

The plugin uses the [SM-2 algorithm](https://en.wikipedia.org/wiki/SuperMemo#Description_of_SM-2_algorithm):

- New cards start with a 1-day interval
- "Good" ratings increase the interval (1 day -> 6 days -> ~15 days -> ...)
- "Again" resets the card to 1 day
- Cards with 21+ day intervals are considered "mature"

The result: you review new/hard cards frequently and easy cards rarely, maximizing retention per minute spent.

## Plugin structure

```
claude-flashcards/
  .claude-plugin/plugin.json    -- plugin metadata
  commands/flashcards.md        -- /flashcards command
  commands/review.md            -- /review command
  skills/session-flashcards/    -- skill: teaches Claude card generation
    SKILL.md
  scripts/flashcards            -- Python data layer (~130 lines)
```

## License

MIT
