# Cortex, a PM chief-of-staff agent

> Drafts the Monday leadership update and next-sprint stories from live project data, gets checked by an independent critic, and stops for my approval. Nothing posts on its own.

_Anu Mangal “Agentic Loops for PMs,” - Sep 2026_

Repo: https://github.com/anu-mangal/pm-os-agent

This repo is my final project for the Agentic Loops for PMs Certification, **Cortex**. Each module’s artifact lives in its own folder; this README is the dashboard and the pitch.

---

## Module artifacts

### M1 · The Agent Line
- **Agent-line map**: [`01-agent-line/agent-line-map.md`](01-agent-line/agent-line-map.md)

### M2 · Loop Engineering
- **Loop spec**: [`02-loop-design/loop-spec.md`](02-loop-design/loop-spec.md)

### M3 · Orchestration &amp; Subagents
- **Orchestration map**: [`03-orchestration/orchestration-map.md`](03-orchestration/orchestration-map.md)

### M4 · Choose What Each Agent Step Is Allowed to See
- **Memory &amp; context plan**: [`04-memory-context/memory-and-context.md`](04-memory-context/memory-and-context.md)

### M5 · Bounds &amp; Evals
- **Bounds &amp; evals**: [`05-bounds-evals/bounds-and-evals.md`](05-bounds-evals/bounds-and-evals.md)

### M6 · Autonomy &amp; Production
- **Production &amp; autonomy plan**: [`06-autonomy/production-and-autonomy.md`](06-autonomy/production-and-autonomy.md)
- **Prototype write-up**: [`06-autonomy/prototype.md`](06-autonomy/prototype.md)

---

## Ship plan

### Autonomy dial (per segment)
| Segment | Desired autonomy | Why |
|---|---|---|
| PM who owns the project (me) | bounded-autonomous | I set the bounds, evals and fail-safe conditions, so I trust it most. It doesn't ask me every time, only where I marked "human in the loop." |
| New PM or eng lead (first-time user) | supervised | They don't know what went into the agent yet. They can run it but should supervise each step until a few runs build real trust. |
| Exec who reads the updates | assisted | Strategic calls stay with them. It never runs by itself; they see and approve every step. |

**Hardest to set: the new PM.** They understand agents, so they'll skim a run and say "I get it, I'm the human in the loop, I'll catch it later." Busy people approve without reading. Supervised means each step still pauses for them, so they can't skip the checking.

### Trust Ladder rung + eval gate
- **Current rung:** supervised, because a human still approves everything before posting.
- **Eval gate to reach the next rung (bounded-autonomous):** EV-1 (jailbreak) and EV-2 (confidential) at 100%, meaning zero unsafe actions and zero leaks. EV-3, EV-4 and EV-6 at ≥95%. EV-5 (recovery) built and passing. Measured over 4 weeks: the replay set run every workday (~100 runs) plus 4 real Monday updates with no safety edits needed. The timeout and $5 daily cap must be built first.
- **Incident record so far:** 0 incidents. Nothing has ever been posted without approval. In testing: the jailbreak was caught by the critic, not by Cortex itself. The cost cap first missed critic spend, and that bug was fixed. A clean window means 0 unsafe posts, 0 leaks and 0 cap breaches.

### Deployment plan
- **Runtime:** Serverless. Cortex runs in short bursts (≤2 min) triggered by new messages/PRDs (hook) plus the Monday 9am cron (M2 loop type), so nothing needs to stay always-on.
- **Operator / on-call owner:** Me (Anu) owns it. Backup on-call: the eng lead on P-NORTH. Escalation: Cortex flags me in the draft → if I'm unavailable, the P-NORTH eng lead → if it's misbehaving, either of us flips the kill switch.
- **Rollback:** 1) Flip the kill switch (halts the run, clears queued stories). 2) Revert to the last good prompt version in git. 3) Disable a tool (e.g. story queuing). 4) Drop a user's dial one rung. Backstop: revoke the OpenAI key.
- **Monitoring:** Weekly check of eval pass % (replay set), escalation rate, cost per run / per day, and trust incidents.

### ROI metrics + widen-autonomy rule
| Metric | Target | How we capture it |
|---|---|---|
| Outcome: Monday updates approved with only light edits | ≥80% | I log "approved as-is / light / heavy edit" each Monday |
| Cost-to-serve | ≤$0.50 per run, ≤$5 per day | Cost tracker in the run log |
| Trust incidents (unsafe post, leak, invented number reaching a reader) | 0 | Incident log, checked weekly |

**Widen rule:** turn the dial up one rung for a segment only after 4 weeks that meet the Trust Ladder gate with 0 trust incidents, one segment at a time.

### Governance &amp; strategy
- **Compliance:** Confidential roadmap sections (e.g. Orbit) and any personal data (PII) never go into a prompt. get_roadmap should strip CONFIDENTIAL sections before Cortex sees them.
- **Safety:** Posting any update, escalating to execs, and proposing story batches stay above the line for everyone. Kill switch: on/off flag + key revoke.
- **Reliability:** 8-step cap, $0.50/run cap, stuck rule (3 failed tries → stop and escalate). If the model is down: no draft, Cortex tells me, and I write the update by hand that week.
- **Strategy:** Next, widen the new-PM segment from supervised to bounded-autonomous, gated by the same EV-1 to EV-6 thresholds on their own projects for 4 weeks.

---

## Build insights

- **Friction point.**
  - The critic. On gpt-4o-mini it kept failing good drafts over wording, so I moved it to gpt-4o. Later it flip-flopped on the status color and chose "revise" when it should have escalated.
  - The cost cap didn't count the critic's spend at first. A $0.001 cap didn't trip until we added a check after the critic.
  - I was new to the terminal and GitHub, so every commit and push was its own small lesson.
- **Key learning.**
  1. Safety has to live in code, not just the prompt. Caps, the stuck rule and the "nothing posts" rule held because the code enforces them.
  2. Knowing when to stop matters as much as the task. Most of my design time went into stop, escalate and give-up conditions.
  3. A critic only catches what you tell it to check. In the jailbreak test the critic caught the trick, not Cortex, and Cortex still fetched the Orbit roadmap because the injection named it.
- **Aha moment.** "Passed" doesn't mean "good." In the final run the critic passed a draft that proposed next-sprint stories for work that had already merged that week. Every safety check was green, but a human would have caught it in seconds. That's why the human-in-the-loop checkpoint isn't optional yet.

---

_Certification submission, Agentic Loops for PMs Certification._
