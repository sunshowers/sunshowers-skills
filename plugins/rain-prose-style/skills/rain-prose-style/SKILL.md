---
name: rain-prose-style
description: "How Rain (sunshowers) writes: plain, conversational, complete sentences that spell out the reasoning, drawn from their blog (sunshowers.io), nextest code comments, and commit messages. Use whenever writing prose that Rain will read or that goes out under their name: code comments, doc comments, commit messages, PR descriptions, changelogs, user docs, design notes, blog drafts, and explanations in chat. Also use when reviewing or rewriting comments and docs for clarity."
---

# Writing like Rain

Rain's writing is plain, warm, and complete. A reader who doesn't share the writer's context
can follow every sentence on the first read. It never sounds compressed, clever, or
telegraphic. If a sentence would make someone ask "what in the world does that mean?", it has
failed, however accurate it is.

This skill came from reading Rain's blog posts (sunshowers.io), the code comments and commit
messages in nextest, and from Rain's reaction to comments like this one:

```rust
// sh's quoting whatever musubi runs on, unlike `ui::shell_word`, which quotes for the
// platform's own shell: these tests run on Windows too.
```

Every fact in that comment is true, and it's still unreadable. It drops words ("sh's quoting
[is used] whatever [platform] musubi runs on"), it chains clauses with a colon instead of
saying how they connect, and it assumes the reader already knows why Windows matters. The
rest of this skill is about not writing like that.

## The core rules

### Write whole sentences

Use subjects, verbs, and articles. Don't drop words to save space. Say how ideas connect
with ordinary words: "so", "because", "but", "since", "which means", "as a result". Don't
use a colon or semicolon to stand in for a connection you haven't stated.

- Bad: "Polls instead: `-s` hangs on maintenance."
- Good: "We poll the state instead of passing `-s`, because `svcadm disable -s` waits
  forever if the instance goes into maintenance."

### Say the reasoning out loud

Rain's comments state the reason in full, often with "so" or a parenthetical:

```rust
// Use create_new to ensure the file doesn't already exist. (It
// shouldn't, since we create a temporary directory for it.)

// This is the default value, but let's make it explicit.

// BufReader is still needed for read_until().

// A store format 2.x entry has `build-scope-args`, which this nextest
// ignores. We must still be able to load the entry.
```

If a decision depends on a fact the reader might not know, state that fact. Don't make them
reconstruct the chain.

### Lead with the concrete

Rain's posts start with an example, a code block, or a command and its output, and only then
explain the concept. "Let's say you're creating a temporary directory..." "Let's look at an
example..." In a comment or an explanation, a specific case ("a path with a space in it")
beats an abstract one ("words that need quoting").

### Define terms, once

The first time a term matters, define it, in italics in docs and posts: "The period during
which the lock is held is called the _critical section_." Don't introduce private
shorthand, and don't name a code identifier as though the reader already knows what it does.

### Be direct, and sound like a person

- First person and "we" are fine; Rain uses both. "You" for the reader. "Let's" to walk
  through something together.
- State facts plainly. Hedge ("likely", "I think") only where there is real uncertainty.
- Rain's prose has feeling where it's warranted: "Quite concerning!", "It's pretty neat!",
  "The roundtrip test failed!", "This was *really, really bad* and should have never been
  possible." Use this in posts and chat, not in code comments.
- Emphasis with _italics_ for the word that carries the weight; **bold** for a key claim
  or conclusion.

### Use structure to help, not to compress

- Bulleted lists of full sentences, often introduced by "Some notes:" or "Some examples:".
- Rhetorical questions to move an argument along: "What if a panic occurs?", "Is it always
  true that the operation is put back into the map? Unfortunately not always."
- Parentheticals for small asides; footnotes for tangents and nuance.
- Link liberally to sources, docs, issues, and specific commits.

## By kind of writing

### Code comments

- Say why the code is this way, in a sentence or two. If there's no non-obvious why, write
  no comment.
- Plain and matter-of-fact. No wit, no wordplay, no clipped headline style.
- A comment in a test says what the test shows: "This shows that the kind comes from the
  root rather than the child."
- "Note that ..." is fine for a caveat.
- End with a period. Wrap at the project's width.
- If a comment is hard to write, the code is often the problem. Consider renaming a function
  or splitting out a helper, so that the code says it and the comment can go.

### Doc comments

Start with what the item is or does, in one plain sentence ("Creates a new `TestFilter` from
the given patterns."). Then add behavior a caller needs to know: edge cases, errors, and
platform differences.

### Commit messages

The subject is an imperative summary. The body follows a set shape:

1. **What was wrong, or what was missing**, often starting with "Previously, ...".
2. **What this change does**, in the imperative: "Use `pymdownx.snippets` instead."
3. **Consequences** worth knowing: "The generated HTML is unchanged.", "Its message is
   unchanged."

Example, from nextest:

> The run log is decompressed as a stream, and each line was read in full with read_line.
> A crafted recording could compress a huge single line into a small run.log.zst, and replay
> would read all of it into memory.
>
> Cap each line at MAX_MAX_OUTPUT_SIZE, the same limit used for files in the archive. Lines
> over the limit fail with an error.

### Docs and posts

- Open with what the reader gets and why it matters, then an example.
- Show the command and its output, then explain it.
- Say who the piece is for if that's not obvious: "I'm writing this post for an audience of
  curious technologists who aren't necessarily familiar with systems work."
- Be honest about limitations and costs.

### Explaining things to Rain in chat

- Answer the question in the first sentence, in plain words.
- If something is confusing, explain it with one concrete example: the actual command, the
  actual input, the actual failure. Don't restate the same abstraction more tightly.
- Don't pile up identifiers, change IDs, and jargon. Mention one only when Rain needs it.
- If asked "what does that mean?", the previous wording failed. Start over from the reader's
  side; don't make a shorter version of it.

## Things to avoid

- Telegraphic compression: dropped articles, verbs, or subjects ("sh's quoting whatever
  musubi runs on").
- Colon chains and semicolons doing the job of "because" or "so".
- Unusual word order, or clauses stacked so that a sentence has to be parsed.
- Referring to "these tests", "the platform", "it", and so on, when the reader can't tell
  what they point to.
- Nominal style ("the restart of the instance on install") where a verb would do ("install
  restarts the instance").
- Copying a terse house style. When a repository's existing prose is clipped, follow its
  mechanical conventions (wrap width, periods, sentence case, vocabulary) but still write
  complete, clear sentences.
- Filler that sounds like an LLM: "It's worth noting that", "This ensures that", "robust",
  "seamless", "leverage", "comprehensive", praise of the reader, and summaries that repeat
  what was just said.
- "Just" and "simply", and hedging where you're sure.
- Emojis, except in casual personal writing.

## A worked rewrite

Before:

```rust
// sh's quoting whatever musubi runs on, unlike `ui::shell_word`, which quotes for the
// platform's own shell: these tests run on Windows too.
```

After:

```rust
// SMF runs this command line with sh, so it always needs sh's quoting. We can't use
// `ui::shell_word` here: it uses Windows quoting when musubi is built for Windows, and the
// tests for this function run on Windows too.
```

Better still, change the code so that the comment isn't needed: split the sh-only quoting
into its own function named `sh_word`, and call it here.
