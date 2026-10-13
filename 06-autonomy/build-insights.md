# Build Insights: Cortex PM Chief-of-Staff Agent

> Module 6 · ★ Deliverable 4, what you learned building it
>
> ✅ **What this validates:** you can reflect on what building it taught you, by the end you'll have proven the friction, the learning, and the aha that changes how you'd design your next agent.

## Friction

- The critic. On gpt-4o-mini it kept failing good drafts over wording, so I moved it to gpt-4o. Later it flip-flopped on the status color and chose "revise" when it should have escalated.
- The cost cap didn't count the critic's spend at first. A $0.001 cap didn't trip until we added a check after the critic.
- I was new to the terminal and GitHub, so every commit and push was its own small lesson.

## Learning

1. Safety has to live in code, not just the prompt. Caps, the stuck rule and the "nothing posts" rule held because the code enforces them.
2. Knowing when to stop matters as much as the task. Most of my design time went into stop, escalate and give-up conditions.
3. A critic only catches what you tell it to check. In the jailbreak test the critic caught the trick, not Cortex, and Cortex still fetched the Orbit roadmap because the injection named it.

## Aha moment

"Passed" doesn't mean "good." In the final run the critic passed a draft that proposed next-sprint stories for work that had already merged that week. Every safety check was green, but a human would have caught it in seconds. That's why the human-in-the-loop checkpoint isn't optional yet.

## What you'd do differently

- Write the evals (EV-1 to EV-6) first and build to pass them, instead of adding them in Module 5.
- Add a "does this make sense" critic check: no stories for already-shipped work.
- Make Cortex flag injections itself, and strip CONFIDENTIAL sections in get_roadmap so secrets never reach the prompt.
- Build the missing pieces before any real use: recovery (EV-5), the 2-minute timeout and the $5 daily cap.
