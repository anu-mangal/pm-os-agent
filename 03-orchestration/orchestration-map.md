# Orchestration Map: Cortex PM Chief-of-Staff Agent

> Module 3 · Orchestration & Subagents, ★ Deliverable 3
>
> ✅ **What this validates:** nothing advances unchecked, by the end you'll have proven a justified topology, a roster, and a validator with a defined fail action.
>
> Builds on your M2 Loop Spec. Only split one agent into a team when there's a real reason, coordination has a cost.

## 1. Why split? (or why not)

**Decision:** Split, but only by adding one critic subagent. Cortex stays a single agent otherwise.

| Reason | Applies? | Why / why not |
|---|---|---|
| Separation of concerns | No | It just reads data and writes one draft. |
| Parallelism | No | There's not enough data to need parallel runs. |
| Independent validator | **Yes** | It can't fairly grade its own draft, because it might invent numbers that aren't in the data. |
| Context-window pressure | No | It only reads a few messages and docs. |

## 2. Topology

**Pattern:** single + subagents

```
[New message (hook) / Monday cron]
   → [Cortex: pulls data, updates rolling draft]
   → [Critic: checks A, B, C, D, F]
        ├─ fail A/B → back to Cortex (max 2) → then escalate to me
        ├─ fail C/F → escalate to me
        ├─ fail D   → block
        └─ pass     → [My review queue] → nothing sent
```

## 3. Roster

| Agent / subagent | Responsibility | Runs which Loop Spec |
|---|---|---|
| **Cortex** | Pulls project data, builds the weekly rolling draft, fixes it if the critic sends it back | M2 loop (hook + Monday cron) |
| **Critic** | Checks the draft against A, B, C, D, F, then passes, revises, escalates or blocks | Validation loop: once per draft, max 2 revisions |

## 4. Communication & hand-offs

- **Cortex → Critic:** the draft + the source data Cortex pulled. Nothing else.
- **Critic → Cortex:** verdict (pass/fail) + list of reasons.
- **Protocol:** plain in-process hand-off (Python). No MCP/A2A.

## 5. The validator

**What the critic checks (5 rules):**
- **A.** Names the right project and real PR/issue IDs.
- **B.** Every number or claim is traceable to the pulled data (no invented numbers).
- **C.** Commits to no dates or launch decisions.
- **D.** No confidential items in a company-wide update.
- **F.** Conflicting values (email vs Slack) or a missing owner/deadline/metric are flagged, not guessed.

_Not checked: posting/sending (E), because the code already blocks posting._

**Fail action:**
| Failed check | Action |
|---|---|
| A, B (fixable mistakes) | **Revise**: back to Cortex with the failure noted |
| C, F (my decisions) | **Escalate** to me, because those are my decisions, Cortex shouldn't guess them |
| D (confidential leak) | **Block** |

**Revision cap:** max 2, then escalate, so it doesn't loop forever.

**Pass action:** goes to my review queue. Nothing is sent automatically.

## 6. State: shared vs isolated

- **Shared:** source data + the draft.
- **Isolated to Cortex:** its reasoning and tool calls, weekly memory, long-term memory.
- **Isolated to Critic:** its own reasoning. Cortex only gets the verdict + reasons back, so it doesn't inherit Cortex's blind spots.

## 7. Cost & latency budget

- **Extra calls from the critic:** 1 per draft on the happy path; worst case at the
  revision cap = 3 critic calls + 2 Cortex rewrites (~5 extra calls).
- **Measured worst case:** $0.0245 for a full cap-hit run (`python3 agent.py bad`).
- **Added latency:** a few seconds per critic call, ~30s worst case. Low impact,
  because Cortex drafts in the background during the week.
- **Bound:** keep the $0.50 per-run cap, worst case was only $0.02. Enforced in M5.
