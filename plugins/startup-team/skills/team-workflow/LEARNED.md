# Learned rules

Rules the team picked up while working, each approved by the owner (1) before it was added.
One line per rule, newest last, in generic words: no project, person, address or secret.
They rank below the owner's words and the project's CLAUDE.md, and above the agent files' defaults.

The main session reads this file when it loads the team-workflow skill and puts the lines for an
agent (and the `all` lines) into that agent's brief.

Format: `- YYYY-MM-DD [agent | all]: <the rule>`

