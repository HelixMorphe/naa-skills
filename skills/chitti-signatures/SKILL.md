---
name: chitti-signatures
description: Gate 3 of the chitti workflow — pin down files, types, method signatures, call stacks and the test plan before any implementation exists. Produces 03-signatures.md. The one document the user reads personally. Invoked by the chitti orchestrator.
---

# Gate 3 — Signatures

This is the gate everyone skips, and the one that pays for the rest of the workflow.

Everything here is a decision an agent would otherwise make silently, halfway through
writing code, where changing it costs a rewrite instead of a sentence.

## This is the user's mandatory read

Gates 1 and 2 can be summarised. **This one they read themselves**, and it should take
about sixty seconds. That minute is what buys the right to skip every diff afterwards —
it's the only remaining place where they keep the map of their own codebase.

So optimise the document for *being read fast by a human who knows this system*:

- Signatures only. **No implementation bodies.** A body is something to skim; a
  signature is something to judge.
- Ordered so the important decisions come first, not alphabetically.
- Short enough that reading all of it is realistic. If it can't be read in a minute,
  the slice plan is too big — say so rather than shipping an unreadable doc.

## Dispatch, then edit

Send a `signature-designer` subagent to draft from `02-architecture.md` and the actual
code. It has its own context for reading the repos.

When it reports back, **edit its output down** before showing the user. A draft that
lists forty signatures because forty exist is a draft nobody reads. Lead with what the
user would want to argue with.

## Produce `03-signatures.md`

```
# Signatures: <feature name>

## Files
<every file created or changed, one line each on why it belongs there>

## Types & signatures
<code blocks — types and method signatures, NO bodies.
 A reader should say "right" or "wrong" in seconds.>

## Call stack
<for each main flow: what calls what, top to bottom, across repos>

## Test plan
<test names and what each asserts — before any of them exist>

## Least confident decisions
<numbered. The calls most worth challenging now, while changing them is free.>
```

## The least-confident list is not optional

An empty "least confident" section means the designer didn't look hard enough, not that
the design is flawless. Push back and get real entries.

This list is the highest-value part of the document: it is the agent telling you where
it guessed. Those guesses become bugs three slices later, when they are expensive.
Surface them to the user explicitly — they are exactly the kind of judgment call the
user is here to make.

## Close the gate

Run the approval protocol. Ask specifically whether the least-confident calls are right,
not just whether the document is acceptable.

Once approved, this document becomes the contract the `drift-checker` measures the
implementation against. Anything the code does that contradicts it is drift, and drift
interrupts the user — so it's worth getting right here.
