---
name: chitti-architecture
description: Gate 2 of the chitti workflow — choose how a feature fits the existing system, via competing architecture proposals stress-tested by adversarial critics. Produces 02-architecture.md with per-repo sections. Invoked by the chitti orchestrator.
---

# Gate 2 — Architecture

The user wants to be genuinely involved here, not handed one option to rubber-stamp.
So your job is not to produce *an* architecture — it is to produce a **real choice**.

## Never design against an imagined codebase

Every proposal must come from reading the actual repos this feature touches (listed in
`00-status.md`). A design that assumes a structure the code doesn't have is worse than
no design: it looks authoritative and it is wrong.

## Fan out: three architects, different priors

Dispatch three `architect` subagents **in parallel**, each with a deliberately different
bias so their proposals actually diverge:

| Prior | Optimises for |
|---|---|
| Smallest change | Fewest files touched, least new surface, ship soonest |
| Cleanest seam | Right boundary even if it costs more now — see the `codebase-design` skill |
| Risk first | The riskiest assumption proven earliest, even if the shape is awkward |

Three architects reaching the same answer is a strong signal. Three reaching different
answers is the *useful* case — that's a real decision surfacing, and it's exactly what
the user asked to be involved in.

## Then refute, don't review

Dispatch `design-critic` subagents against the proposals. Prompt them to **refute** —
a critic asked to "review" will produce agreeable notes that catch nothing.

Give critics distinct lenses rather than running identical ones. Something failing on
correctness, on operational cost, and on migration difficulty are three different
failures, and redundant reviewers only find the first.

Architects can spawn their own helpers (nesting is allowed to depth 3), so a proposal
needing a wiki fact can ask the `domain-librarian` directly without routing through you.

## What you bring back

**Not three documents.** The user's time is the scarce resource. Bring:

1. **One recommendation**, with the reason it won in a sentence or two.
2. **The genuine disagreements** — where architects or critics actually split, framed
   as a decision the user makes. Two or three at most. If you have seven, you haven't
   done the synthesis yet.
3. **What every proposal agreed on**, in one line. Consensus needs recording, not debating.

Anything a critic refuted is dead. Don't present a killed option as a live one for the
sake of appearing balanced.

## Produce `02-architecture.md`

```
# Architecture: <feature name>

## Fit
<which existing services/modules this touches, and how>

## Per repo
### <repo-name>
- Endpoints: <route + verb + purpose — or none>
- Data: <new/changed tables, with outlines of the queries that will hit them>
- Changes: <what lands here and why here>

## Flow
<end-to-end call order across repos for the main path: what calls what>

## External
<third-party APIs, env var NAMES (never values), webhooks — or none>

## Decided
<the contested calls, and what was chosen — so nobody relitigates them in three weeks>

## Rejected
<options killed by critics, and what killed them — equally important>
```

The per-repo split is not cosmetic. A feature here spans backend, frontend, and SDK,
and a slice isn't done until every repo it touches verifies together.

## Close the gate

Run the approval protocol. If the architecture proves the product ask is unbuildable
as specified, **stop and reopen Gate 1** — don't quietly redesign the product inside
an architecture doc where nobody will find the decision later.
