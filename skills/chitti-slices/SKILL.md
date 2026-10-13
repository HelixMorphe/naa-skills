---
name: chitti-slices
description: Gate 4 of the chitti workflow — plan vertical slices, then build them one at a time with independent verification and drift-checking. Runs exception-based, interrupting the user only when the spec itself is in question. Invoked by the chitti orchestrator.
---

# Gate 4 — Vertical slices

Code, finally. One thin end-to-end slice at a time, each ending in a state that runs
and can be proved to run.

**Banned: horizontal building.** All the database, then all the services, then all the
API, then the frontend, with nothing testable until the end. That's how a 2,000-line
diff lands in someone's lap, which is the outcome this whole workflow exists to prevent.

## The slice plan

Write `04-slices.md` first — build order, one entry per slice:

```
## <n> — <name>
- Repos: <every repo this slice touches>
- Proves it works: <the exact command a verifier will run>
```

Rules:

- **Slice 1 is a tracer bullet.** Mocked or hardcoded, stubbed UI, wired end to end.
  It does almost nothing — but it runs, and the user can see it.
- **Slice 2** replaces the mocks with real logic for the single happy path.
- **Slice 3+** — one capability each: a business rule, an error case, an edge case, polish.
- Every slice names its verification command up front. A slice with no way to prove it
  works is not a slice; it's a hope.
- **A slice may span repos** and isn't done until all of them verify together.

The verification command usually comes from the manifest's `.verification` block.

## Building one slice

Per slice, in order:

1. **Implement.** Dispatch `slice-implementer`. Use `isolation: "worktree"` when
   parallel slices could touch the same files. Drive `tdd` at the seams named in the
   test plan.
2. **Verify.** Dispatch `slice-verifier` — **always a fresh agent, never the implementer,
   never continued from a previous verification.** A verifier that remembers arguing
   with an implementer starts sympathising with it. Independence is the entire value.
3. **Drift-check.** Dispatch `drift-checker` against the approved `03-signatures.md`.
4. **Record.** Write the evidence into `verification/slice-<n>.md` and tick the box in
   `00-status.md`.

Verification runs in the background while you prepare the next slice — don't idle.

**Models.** `slice-implementer` and `slice-verifier` run on **Sonnet** — that is set in
their own definitions, so don't pass a `model` override when dispatching them. Gate 4 is
the token-heavy gate: the hard thinking already happened in Gates 2 and 3, and building
against an approved signatures document is execution, not design. Let them inherit their
own default.

The exception is rung 3 below — a fresh implementer after three failures is no longer
executing a clear spec, so spawn that one with `model: "opus"`.

## The escalation ladder

This is what makes the loop exception-based, and it's the whole reason the user can stop
watching. Follow it exactly:

| Rung | Situation | Action |
|---|---|---|
| 1 | Verification fails | **Continue** the same implementer with the failure report |
| 2 | Fails again | Continue again, with the full failure history made explicit |
| 3 | Fails a third time | **Fresh** implementer on **Opus**, handed a written handoff (`handoff` skill) |
| 4 | The fresh agent fails too | **Interrupt the user** |

Rungs 1–2 continue the same agent because it already knows the code it wrote and the
dead ends it ruled out; a fresh agent would re-walk one of them. Rung 3 switches because
three failures means the agent is **anchored, not confused** — it has invested in an
approach and will defend it with increasingly elaborate patches. You cannot argue an
agent out of its own sunk cost; you can only replace it.

Rung 4 is different in kind. A blank-slate agent failing against a clear spec means
**the spec is wrong, not the code.** That's a Gate 3 problem and it is genuinely the
user's call. Say so plainly when you interrupt — don't present it as an implementation
problem you couldn't crack.

## When to interrupt, otherwise

Only these:

- Rung 4 above.
- **Drift** — the code contradicts the approved signatures. Show the contradiction, not
  a summary of it.
- An **irreversible action** — pushing a branch, opening a PR, filing a ticket. Always,
  regardless of autonomy.
- The user hasn't looked at code in a long stretch. Nudge once at a slice boundary.
  Losing touch with the codebase costs weeks, exactly when an agent hits a bug it can't
  solve — and that is the moment they'll need to read it themselves.

Everything else: proceed, and record what you did so it can be audited afterwards.

## Trusting verification

The verifier's verdict is only worth what the check behind it is worth.

A green that came from a stack that didn't fully start, migrations that didn't run, or
a probe that couldn't reach the service is **worse than a red** — the user stops reading
diffs on the strength of it. If a verification is inconclusive, report it as inconclusive.
Never round it up to a pass.
