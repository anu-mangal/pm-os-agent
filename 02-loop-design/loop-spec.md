# Loop Spec: Cortex PM Chief-of-Staff Agent

> Module 2 · Loop Engineering, ★ Deliverable 2
>
> ✅ **What this validates:** the agent knows when to run and when to stop, by the end you'll have proven a one-page Loop Spec with a trigger, a definition of "done," and explicit stop conditions.
>
> Your one-page blueprint for how the work you handed to the agent (M1) actually *runs*.
> An agent is just a prompt that fires itself, this spec says when it fires, what "done" means, and what it needs to do the job. Living document; refine as the course progresses.

## 1. Trigger & loop type

**Chosen type:** Hook (primary) + Monday 9am cron (backup)

**Why:** In a real work situation it's better that Cortex keeps drafting the update as new messages or PRDs arrive, and on Monday we do a quick check, see whatever was missed, and finalize the draft. That way we're not starting fresh on Monday and running into tools breaking, missing items, or long run times. The human in the loop stays aware of the draft as it shapes up, so there are fewer surprises when it's due.

**Ruled out:**
- **Heartbeat:** I don't want it checking constantly and wasting tokens.
- **Goal:** the finish line is simple (a draft queued for review), so Cortex doesn't need to retry until it proves a target was met.

**Dedupe:** Cortex keeps a list of message IDs it has already handled and skips repeats, and keeps only one rolling draft per week, so a repeat event updates that draft rather than creating a new one.

## 2. Goal / definition of done

**Per hook run:** the new message's info is added to the one rolling draft for the week and queued for my review. Nothing is sent.

**Monday cron run:** the full status update has passed the critic and is queued for my final approval. Nothing is sent.

## 3. Stop conditions

| Condition | What it looks like | What happens |
|---|---|---|
| **Success** | The critic passed the draft and it's in my review queue | Stop at the HITL checkpoint. Nothing posted |
| **Stuck / give up** | A tool fails or returns empty data 3 times, OR the run hits the $0.50 cost cap | Stop, log what was missing, flag it in the draft |
| **Escalate to human** | (1) Email and Slack give different values for the same thing. (2) The owner or deadline is empty, so it would have to guess. (3) The owner hasn't defined a measurable target metric (e.g. we don't know if it's click-through rate, activation rate or units sold). (4) M1 checkpoints: a story batch is proposed, a choice of what to escalate to execs, or posting the update | Stop, mark the issue in the draft and ask me (e.g. "What is the real metric to look for here?"). I own the decision |

## 4. State

**Weekly memory (cleared after Monday approval):** the one rolling draft for the week and the list of message IDs already handled. It's cleared because everything it gathered is already in the Monday draft. The hooks are only there to be extra prepared, so a failure on Monday doesn't leave a blank draft.

**Long-term memory:** roadmap, past updates, team norms, and decisions humans approved. This is what's needed for next week.

**Scope:** per project. One project's info never leaks into another project's update.

## 5. The five things a loop can lean on

| Component | For Cortex |
|---|---|
| **Work tree** (isolated workspace per run, a git worktree) | Not needed yet, because Cortex only reads data and writes one draft, so runs can't step on each other's files. |
| **Skills** (reusable capabilities) | Not needed yet, because the update format lives in the prompt. Make it a skill once a second agent needs the same format. |
| **Plugins / connectors** (tools & access, optional if you don't have one yet) | Planned, read-only: Slack + email (feed the hook), Google Docs (PRDs, past updates), product admin dashboard (metrics). Not wired yet, because the build uses sample data for now. |
| **Subagents** (independent check when the loop can't grade itself) | Already have one: the critic grades the draft so Cortex isn't grading itself. More in M3 orchestration-map.md. |
| **State tracking** | Weekly rolling draft + handled message IDs (cleared after Monday approval), plus long-term memory. See §4. |

> Context plan (M4) and the hand-off to bounds & evals (M5) come in later modules, you'll add them to their own deliverables then, not here.

## Link to live loop

[`00-build/agent.py`](../00-build/agent.py)
