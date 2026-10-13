---
name: chitti-product
description: Gate 1 of the chitti workflow — establish what problem is being solved and how success is measured, through a grilling session. Produces 01-product.md and screen mockups. Invoked by the chitti orchestrator; not usually run alone.
---

# Gate 1 — Product

The goal is a decision about **what to build and why**, made before anything constrains
it. Everything technical is out of scope here, deliberately.

## Start the librarian first

Before the conversation begins, dispatch the `domain-librarian` subagent in the
background with the feature description. Ask it for: what exists today in this area,
the vocabulary this product uses for it, and anything already documented that overlaps.

It has its own context window, so it can read broadly without costing you any. Don't
block on it — carry on grilling and fold its brief in when it lands. Put anything it
reports as *not documented* or *contradictory* straight into `wiki-gaps.md`.

## Run a grilling session

Use the `grilling` skill. Work the design tree in rounds: ask the whole frontier at
once, numbered, each with your recommended answer, then wait.

The user chose this workflow to spend *more* time here. This is the part they want.
Don't rush it, and don't accept a vague answer to move things along — a soft answer
now becomes a rewrite later, which is the exact cost this whole system exists to avoid.

### Tech talk is banned here

If databases, schemas, endpoints, file layouts, or libraries come up — **park them.**
Write them into a `## For Gate 2` note in `00-status.md` and return to the product
question.

This is not pedantry. The moment implementation is in the room, requirements get
quietly trimmed to what sounds easy, and nobody notices it happening. If the user
pushes into architecture anyway, say plainly that it's the next gate and that you
have written their point down so it isn't lost.

## Produce `01-product.md`

```
# Product: <feature name>

## Problem
<the user's problem, in the end user's words — not a developer's>

## Success metric
<one number tied to the business, and how it gets measured>

## Announcement
<3-6 sentences announcing this to users. If you cannot write it, it is the wrong thing
 to build — and that discovery is worth more than the doc.>

## Screens
<one line per mockup file, or "no UI">
```

## Mockups

For anything with a UI, produce plain HTML in `mockups/` — one file per screen. No
framework, no build step, throwaway by design.

**Offer variations rather than one draft.** Two or three genuinely different takes on
the same screen let the user *choose*, which is faster and sharper than making them
critique a single option into shape. Iterate until they say "yes, that one."

## Close the gate

Run the orchestrator's approval protocol. Then tell the user this is a clean point to
stop, and that Gate 2's architects are already working in the background.
