# M4 Part A draft (gut calls, no rubric)

## Retrieve vs. long-context

| Source | Call | Why |
|---|---|---|
| get_activity | Retrieve | too big, grows daily |
| search_past_updates | Retrieve | unbounded, only recent ones matter |
| get_roadmap | Retrieve | keeps confidential items out of context |
| get_norms | Long-context | small, every rule applies every run |
| get_task | Long-context | one small static doc |

## Remember / forget

- **Remember:** past updates and prior numbers
- **Forget:** raw tool output

## How it rots

Stale numbers get cited as current.
