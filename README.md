# naa-skills

Claude Code skills and subagents for the **chitti** workflow: a gated feature process that goes product → architecture → signatures → vertical slices, with an approval gate between each step.

## Contents

- `skills/chitti`: the orchestrator. Start here.
- `skills/chitti-product`: Gate 1, the problem and how success is measured.
- `skills/chitti-architecture`: Gate 2, competing proposals tested by critics.
- `skills/chitti-signatures`: Gate 3, files, types, signatures and the test plan.
- `skills/chitti-slices`: Gate 4, building and verifying vertical slices.
- `agents/`: the subagents the gates hand work to (architect, design-critic, domain-librarian, drift-checker, signature-designer, slice-implementer, slice-verifier).

## Install

```sh
cp -R skills/* ~/.claude/skills/
cp agents/*.md ~/.claude/agents/
```
