---
description: Propose a rule the team should keep, for my 1/0
---
Load the `team-workflow` skill if it is not loaded. Turn the text below into ONE generic rule (no project name, person, address or secret), run `team.py learn propose` with the turn folder as evidence, and ask me 1 or 0. On 1, run `team.py learn decide <id> 1`, then `team.py learn apply <id> --repo <the startup-team clone>` and commit the two changed files there on a branch `learn/<id>` with a generic message. Do not push it: I merge it.

Rule: $ARGUMENTS
