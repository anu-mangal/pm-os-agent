# Agent Line Map: Cortex PM Chief-of-Staff Agent

> Module 1 · The Agent Line
>
> ✅ **What this validates:** every risky action has a clear owner, by the end you'll have proven an above/below-the-line map with HITL checkpoints, scored on reversibility, blast radius, and measurability.

## The workflow, decision by decision

List every discrete decision or action in your agent's workflow, then score each one and place it **above** the line (a human owns it) or **below** (the agent owns it). Borderline calls get an HITL checkpoint.

| Decision / action | Reversibility (H/M/L) | Blast radius (H/M/L) | Measurability (H/M/L) | Above / Below | HITL? |
|---|---|---|---|---|---|
| Pull project state + activity | High | Low | High | Below | No |
| Decide relevant context | Med | Med | Low | Below | Yes, human checks what was left out |
| Draft the update | High | Low | High | Below | No |
| Decide tone / commitment level | High | Low | High | Below | No |
| Flag at-risk / escalation | High | Low | High | Below | No |
| Choose what to escalate | Low | High | Low | Above | No, human owns it |
| Propose a story batch (capped) | High | Low | Med | Below | Yes, human approves before backlog |
| Post / approve company-wide update | Low | High | High | Above | No, human owns it |

## Agent anatomy (sketch)

- **Model:** Default: gpt-4o-mini (cheap, fast, under 1 cent per run). Switch to a frontier model when the critic keeps rejecting drafts, or for the leadership-facing update where tone matters most.
- **Tools:** Read: project lookup · activity lookup · past-update search · roadmap · team norms. Write: propose stories (capped, queued for approval). No publish tool, so Cortex can't post anything.
- **Memory:** Keep: roadmap, past updates, team norms, and decisions humans approved. Forget: each run's draft and working notes. Every run starts fresh from the data.
- **Loop:** _placeholder, defined in M2 loop-spec.md_
- **Bounds:** _placeholder, defined in M5 bounds-and-evals.md_
- **Evals:** _placeholder, defined in M5 bounds-and-evals.md_

## The golden rule, applied

1. **Pull project state + activity**: sits below the line because it's easy to reverse, has a low blast radius, and is easy to verify. Deciding factor: reversibility, it only reads data.
2. **Decide relevant context**: sits below the line with a human check because it's moderately reversible, has a medium blast radius, and is hard to verify. Deciding factor: measurability, you can't see what was left out.
3. **Draft the update**: sits below the line because it's easy to reverse, has a low blast radius, and is easy to verify. Deciding factor: reversibility, a draft is easy to rewrite.
4. **Decide tone / commitment level**: sits below the line because it's easy to reverse, has a low blast radius, and is easy to verify. Deciding factor: reversibility, it's still a draft a human sees.
5. **Flag at-risk / escalation**: sits below the line because it's easy to reverse, has a low blast radius, and is easy to verify. Deciding factor: blast radius, a flag only raises a hand.
6. **Choose what to escalate**: sits above the line because it's hard to reverse, has a high blast radius, and is hard to verify. Deciding factor: blast radius, it puts problems in front of executives.
7. **Propose a story batch (capped)**: sits below the line with a human approval because it's easy to reverse, has a low blast radius, and is only partly verifiable. Deciding factor: measurability, whether they're the right stories is partly judgment.
8. **Post / approve company-wide update**: sits above the line because it's hard to reverse, has a high blast radius, and is easy to verify afterwards. Deciding factor: reversibility, you can't unsend it.

## Hardest call

**Action 4: Decide tone / commitment level.** I wasn't sure if the commitment level in the draft should be treated as important, or as still a draft that will get edited. **Deciding axis: reversibility.** It's still a draft a human sees before it goes out, so I kept it below the line.
