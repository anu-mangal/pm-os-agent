# Context Engineering & Memory: Cortex PM Chief-of-Staff Agent

> Module 4 · Context Engineering & Memory
>
> ✅ **What this validates:** the agent reasons on the right, safe inputs, by the end you'll have proven a context budget, per-source retrieve-vs-long-context decisions, and a memory map with risk mitigations.
>
> 🗂️ **How the lab maps to this file:** In **Part A** (before the lecture) you don't edit this file, you rough-draft on scratch, focused on the per-source calls in **section 2** plus a quick remember/forget + "how it rots" sketch. In **Part B** (after the lecture) you complete **all five sections**; the Lab Guide's guided builder writes this file for you to copy in and commit.

## 1. Context budget

Each run Cortex receives, in priority order (drop from the bottom first if space is tight):

1. **get_task**: the job itself
2. **get_norms**: the rules
3. **get_activity**: the evidence for every number
4. **get_roadmap (safe slice only)**: what's next
5. **search_past_updates**: prior numbers and tone, dropped first

## 2. Retrieve vs. long-context: per source

For each data source, decide: **retrieve** (narrow a large/changing corpus to the relevant slice) or **long-context** (just include a bounded set you can reason over).

| Source | Size / volatility | Decision | Deciding factor | Why |
|---|---|---|---|---|
| get_activity | large, grows daily | Retrieve | Citation | every number must point to a PR |
| search_past_updates | unbounded, grows weekly | Retrieve | Size | unbounded and grows every week |
| get_roadmap | medium, changes rarely; has confidential items | Retrieve | Audit | keep the secret pages out of context |
| get_norms | small, rarely changes | Long-context | Size | small and every rule applies |
| get_task | tiny, static | Long-context | Size | one tiny doc |

## 3. Retrieval quality plan

| Source | Routing | Grading | Reranking | Self-verification | Caching | Why |
|---|---|---|---|---|---|---|
| get_activity | | | | ✅ | | catches made-up numbers like the 40% |
| search_past_updates | | ✅ | ✅ | | | so old numbers don't win |
| get_roadmap | | ✅ | | | ✅ | secrets never reach the AI; rarely changes, so cache the safe pages |

**Not retrieved (long-context, no checks needed):** get_norms, get_task.

## 4. Memory map (your PM brain)

| Memory type | What Cortex stores | Scope / TTL |
|---|---|---|
| **Working** (in-loop) | Task, tool results, draft, critic feedback | Until a human approves the draft, then wiped |
| **Episodic** (past runs) | Approved status updates and decisions (never rejected drafts) | Roll-up: weeklies ~90 days → monthlies ~1 year → quarterly/yearly long-term. "So I can compile monthly and yearly." |
| **Semantic** (durable facts/prefs) | Durable facts and rules (confidential list, GA gates, norms) | No stored copy; re-read from source every run, "so nothing goes stale." Roadmap cache refreshed when the file changes. |
| **Shared** (across agents) | Only human-approved items (updates, story queue) | Same TTL as episodic; never working memory or secrets |

## 5. Memory risks & mitigations

| Risk | Where it bites Cortex | Mitigation |
|---|---|---|
| **Staleness** | Last month's 39% cited as current | Every memory is dated; reranking puts newest first; semantic facts re-read every run; roadmap cache refreshed when the file changes |
| **Drift** | Roll-up summaries slowly bend the facts (monthly says "~45%") | Roll-ups built only from approved weeklies; numbers cite the original week; human approves each roll-up |
| **Poisoning** | A task brief with a fake number or "ignore the rules" gets saved and trusted forever | Only human-approved items are saved; briefs are data, not instructions (team norms); critic blocks invented numbers |
| **PII / retention** | Pulsar/Orbit or people's names saved in memory or shared | Confidential pages graded out before Cortex sees them; never stored or shared; working memory wiped after approval; TTLs on everything |
