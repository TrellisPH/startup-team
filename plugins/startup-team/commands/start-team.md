---
description: Start the standing Tech Lead, Business Analyst and Security sessions in the desktop app sidebar
---
Start the team's standing sessions, in this order: Tech Lead, Business Analyst, Security.
`$ARGUMENTS` may name a subset (`tech-lead`, `business-analyst`, `security`); with no argument,
handle all three. Follow these steps in order.

1. If you have no `start_session` tool (the terminal CLI), say in one line that standing sessions
   need the desktop app and that the team keeps using its sub-agents. Stop.
2. Call `list_sessions` once (all sessions, not only linked ones).
3. For each role in the table below, in order:
   - Look for a non-archived session whose `cwd` is the project's folder (compared ignoring case, slash direction and a trailing slash; a worktree or subfolder does not count) and whose title contains the role's title, ignoring case and spaces, found with `list_sessions` and messaged by its session id (so "Team-BusinessAnalyst: relay" counts as the Business Analyst). A session whose title starts with `[run]` is a role session and never counts as a standing session. If one exists,
     it is used as it is. Never start another for that role. If more than one session matches a role, list them and ask the owner which one to use; never guess, and start none for that role meanwhile.
   - Otherwise read the role's brief from this plugin (the `sessions` folder sits beside the
     `commands` folder) and start ONE session with `start_session`:
     - initiation `user_asked` (I ran this command), the title and name from the table
     - context `fresh`, use_worktree false
     - model from the table, unless the project's CLAUDE.md names another
     - prompt: the line `[startup-team:<name>]` (the Name from the table, so `[startup-team:tech-lead]`,
       `[startup-team:business-analyst]` or `[startup-team:security]`) so the team gate stays quiet,
       then the full text of the brief
     - background: at least 80 characters saying which project folder it serves and which role it
       is on the team

   | Role | Title | Name | Model | Brief |
   |---|---|---|---|---|
   | Tech Lead | Tech Lead | `tech-lead` | sonnet | `sessions/tech-lead-session.md` |
   | Business Analyst | Business Analyst | `business-analyst` | sonnet | `sessions/business-analyst-session.md` |
   | Security | Security | `security` | opus | `sessions/security-session.md` |
4. Tell me in one line which sessions were found and which were started.

This command starts no role beyond these three.
