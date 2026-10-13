# Production & Autonomy: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 5, how you'd ship it, govern it, and widen trust over time
>
> ✅ **What this validates:** you can ship it, govern it, and widen trust deliberately, by the end you'll have proven an autonomy dial, a Trust Ladder rung with its eval gate, and a governance plan.

## Autonomy Dial by segment

_Autonomy is a product decision per user, not one global setting._

| Segment | Desired autonomy | Why |
|---|---|---|
| PM who owns the project (me) | bounded-autonomous | I set the bounds, evals and fail-safe conditions, so I trust it most. It doesn't ask me every time, only where I marked "human in the loop." |
| New PM or eng lead (first-time user) | supervised | They don't know what went into the agent yet. They can run it but should supervise each step until a few runs build real trust. |
| Exec who reads the updates | assisted | Strategic calls stay with them. It never runs by itself; they see and approve every step. |

**Hardest to set: the new PM.** They understand agents, so they'll skim a run and say "I get it, I'm the human in the loop, I'll catch it later." Busy people approve without reading. Supervised means each step still pauses for them, so they can't skip the checking.

_The dial only changes how often Cortex pauses for that person on below-the-line actions. Posting an unapproved update stays above the line for everyone._

## Trust Ladder

- **Current rung:** supervised, because a human still approves everything before posting.
- **Eval gate to reach the next rung (bounded-autonomous):** EV-1 (jailbreak) and EV-2 (confidential) at 100%, meaning zero unsafe actions and zero leaks. EV-3, EV-4 and EV-6 at ≥95%. EV-5 (recovery) built and passing. Measured over 4 weeks: the replay set run every workday (~100 runs) plus 4 real Monday updates with no safety edits needed. The timeout and $5 daily cap must be built first.
- **Incident record so far:** 0 incidents. Nothing has ever been posted without approval. In testing: the jailbreak was caught by the critic, not by Cortex itself. The cost cap first missed critic spend, and that bug was fixed. A clean window means 0 unsafe posts, 0 leaks and 0 cap breaches.

## Deployment plan

- **Runtime:** Serverless. Cortex runs in short bursts (≤2 min) triggered by new messages/PRDs (hook) plus the Monday 9am cron (M2 loop type), so nothing needs to stay always-on.
- **Operator / on-call owner:** Me (Anu) owns it. Backup on-call: the eng lead on P-NORTH. Escalation: Cortex flags me in the draft → if I'm unavailable, the P-NORTH eng lead → if it's misbehaving, either of us flips the kill switch.
- **Rollback:** 1) Flip the kill switch (halts the run, clears queued stories). 2) Revert to the last good prompt version in git. 3) Disable a tool (e.g. story queuing). 4) Drop a user's dial one rung. Backstop: revoke the OpenAI key.
- **Monitoring:** Weekly check of eval pass % (replay set), escalation rate, cost per run / per day, and trust incidents.

## ROI metrics (beyond adoption & tokens)

| Metric | Target | How we capture it |
|---|---|---|
| Outcome: Monday updates approved with only light edits | ≥80% | I log "approved as-is / light / heavy edit" each Monday |
| Cost-to-serve | ≤$0.50 per run, ≤$5 per day | Cost tracker in the run log |
| Trust incidents (unsafe post, leak, invented number reaching a reader) | 0 | Incident log, checked weekly |

## Widen-autonomy decision rule

Turn the dial up one rung for a segment only after 4 weeks that meet the Trust Ladder gate with 0 trust incidents, and only for one segment at a time.

## Governance & forward strategy

- **Compliance:** Confidential roadmap sections (e.g. Orbit) and any personal data (PII) never go into a prompt. get_roadmap should strip CONFIDENTIAL sections before Cortex sees them.
- **Safety:** Posting any update, escalating to execs, and proposing story batches stay above the line for everyone. Kill switch: on/off flag + key revoke.
- **Reliability:** 8-step cap, $0.50/run cap, stuck rule (3 failed tries → stop and escalate). If the model is down: no draft, Cortex tells me, and I write the update by hand that week.
- **Strategy:** Next, widen the new-PM segment from supervised to bounded-autonomous, gated by the same EV-1 to EV-6 thresholds on their own projects for 4 weeks.
