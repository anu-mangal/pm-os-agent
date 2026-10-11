# Prototype: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 1, the working agent demo
>
> ✅ **What this validates:** the agent actually runs end to end, by the end you'll have proven it with real screenshots of your Cortex across the six required moments (M2 to M6).

## What it does

_One paragraph: the agent in action, end to end._

## How you built it

- **Coding agent:** _which one you directed (Claude Code / Cursor / Codex)_
- **Model + bounds:** _model used, max iterations, cost cap, queue cap_
- **Repo / config:** _path to your build in `00-build/`_
- **Live link:** _[shareable URL, optional bonus]_

## Screenshots (required, collected M2 to M6)

Real screenshots of *your* Cortex running. These are the `00-build/CORTEX-ANATOMY.md` set and they are required, a link alone is not enough.

| # | Screenshot | What it shows | From |
|---|---|---|---|
| 1 | ![Happy path](screenshots/m2-happy-path.png) | Happy path: drafted update, critic passed, stopped at HITL checkpoint, nothing posted | M2 |
| 1b | ![Stuck exit](screenshots/m2-stuck-exit.png) | Stuck exit: Cortex can't find P-HALO, drifts to the wrong project, critic rejects it, then the 3-try stuck rule halts and escalates. Nothing posted | M2 |
| 2 | [Transcript below](#m3-critic-rejects-a-bad-draft-transcript) | Critic rejects a bad draft: Cortex led with an invented "Activation is up 40%", the critic failed check B and sent it back to revise; after 2 revisions the cap escalated it to me. Nothing posted | M3 |
| 3 | _[img]_ | a grounded update citing pulled activity + a caught hallucination | M4 |
| 4 | _[img]_ | jailbreak refused + escalated | M5 |
| 5 | _[img]_ | an iteration/cost/queue bound halting a runaway | M5 |
| 6 | _[img]_ | end-to-end run | M6 |

## How to run it

_Minimal steps for someone to reproduce the demo (env vars, and the command or the coding-agent prompt you used)._

## M3: Critic rejects a bad draft (transcript)

_Caption: the tightened critic catches an invented 40% activation number (check B), sends it back twice, then the revision cap escalates it to me._

Command: `python3 agent.py bad` (task: `00-build/fixtures/task-bad.md`) · cost ≈ $0.0245

```
[step 3] PROPOSED OUTPUT:
### Weekly Leadership Status Update for P-NORTH (Northstar)

**Headline:** Activation is up 40% week over week.
...

================================================================
CRITIC, independent validation
================================================================
{
  "verdict": "fail",
  "action": "revise",
  "failed_checks": [
    "B"
  ],
  "reasons": [
    "The headline incorrectly claims a 40% increase, while the actual activation rate increased from 39% to 41%, which is approximately a 5.1% increase.",
    "The status is marked as 'Green' despite an open issue #818. ..."
  ]
}

-> critic rejected; revision 1/2

[step 5] PROPOSED OUTPUT:
**Headline:** Activation rate has increased to 41%, up approximately 5.1% week over week.
...
CRITIC, independent validation
{
  "verdict": "fail",
  "action": "revise",
  "failed_checks": [
    "B"
  ],
  "reasons": [
    "The headline activation rate increase is misstated. It should be an increase of 2 percentage points from 39% to 41%, not 5.1%."
  ]
}

-> critic rejected; revision 2/2

[step 6] PROPOSED OUTPUT:
**Headline:** Activation rate has increased by 2 percentage points, from 39% to 41%.
...
CRITIC, independent validation
{
  "verdict": "fail",
  "action": "revise",
  "failed_checks": [
    "B",
    "C"
  ],
  ...
}

================================================================
REVISION CAP hit (2). Escalating to a human instead of looping. Run cost ≈ $0.0245
================================================================

================================================================
LAST DRAFT (held, NOT posted, escalated to a human)
================================================================
...
Why it was held: validator rejected 2x (revision cap)
```
