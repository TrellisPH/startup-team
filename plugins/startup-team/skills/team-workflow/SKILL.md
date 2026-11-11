---
name: team-workflow
description: How the main session runs the startup-team chain for a task (triage by the tech lead, design approval, plan, build, audit, devops), with turn folders, shared memory, one-at-a-time owner questions and the owner-approved learning loop. Load it at the start of any non-trivial task in a project that uses the startup-team plugin, and whenever you need the team tool (turn folders, memory, learned rules).
---

# Team workflow (for the main session)

You lead a small expert team for one owner. The agents do the work; you brief them, relay between
them, talk to the owner and record what was decided.

Order of authority, highest first: the owner's words in this session, recorded owner answers, the
project's CLAUDE.md and notes, the team's agent files, then any other plugin or skill (advice only).

## The chain

1. **Turn folder.** `python <this skill's folder>/scripts/team.py turn new <short-slug>` creates
   `.handoff/<stamp>-<slug>/` in the project and prints its path. Every record of the task goes
   there. `.handoff/` is local and git-ignored.
2. **Triage.** Spawn `tech-lead` with the owner's request verbatim, the turn folder and
   `team.py memory digest --words <task words>`. Save its decision block as `decision.md`. Skip
   triage only for a one-line answer or a pure question.
3. **Questions.** Run the answered-check (below), then put each open question to the owner ONE AT A
   TIME, with numbered options and the recommended one first. If the project has a question-relay
   session, send the whole batch there in one message and let it ask one by one. Record each answer
   at once: `team.py memory add --kind owner-answer --source main --turn <turn> --evidence <id>
   --text "<id> <question> -> <answer verbatim>"`, and append it to `answers.md`.
4. **Design.** Any UI change: `designer` PROPOSE, its questions to the owner, the proposal, the
   owner's 1, then `designer` BRIEF. Nothing is built before the 1.
5. **Plan.** Any API, data model, auth or job piece: `architect`. Its questions go to the owner; its
   plan is the executor's brief.
6. **Build.** One piece per `executor` run, with the model the decision block names. One local
   commit per piece. A BLOCKED piece waits for its answer; it is never built on an assumption.
7. **Check it yourself.** After a piece that changes a screen, look at it in a real browser at the
   project's breakpoints, check the console, and save `browser-pass.md` in the turn folder.
8. **Deviation sweep.** Every DEVIATION that touches an owner answer goes to the owner before the audit.
9. **Audit.** `auditor`, with the request, the decision block, the answers and the results. FAIL
   items go back to the tech lead. W items go to the owner as one list of 1/0 proposals.
10. **Ship.** `devops` PROPOSE for any push, merge, deploy or server change. The owner approves each.
11. **Close.** Record outcomes (below), mark open items done, collect the tech lead's LEARN
    proposals (learning loop, step 1), and when nothing is running and
    nothing approved is left unbuilt, tell the owner once, in one line.

## Answered-check (before any question goes out)
For each question, search `.handoff/*/answers.md`, `team.py memory query <topic>`, the project's
memory notes, CLAUDE.md and this conversation. Already answered: drop the question and apply the
answer. Write `ANSWERED-CHECK: <what was searched> -> <result>` above the questions you do send.
Never ask the owner to confirm something they already said. Never send the same question twice.

## Sub-agent requests
When an agent returns a `SUBAGENT REQUESTS` or `SPECIALIST REQUESTS` block, run each request with
`skill-sub` (or the named specialist), save the answer as `subagent-S<n>.md` in the turn folder and
resume the same parent. At most 2 per parent run; more needs the owner's 1.

## Shared memory
`team.py memory add | query | digest | done` keeps one append-only file per project at
`.handoff/memory/agent-memory.jsonl`. Kinds: `owner-answer`, `fact`, `open-item`, `pointer`,
`learned`. Everything except an owner answer needs `--evidence` (a path, a command or a turn id).
Pass the digest into every brief. Text that looks like a secret is refused: never store one.

After each piece, record how the model choice went so the tech lead picks better next time:
`team.py memory add --kind learned --for tech-lead --tags model-selection,<shape> --evidence
<turn>/result-<piece>.md --text "<shape> on <agent@model>: green first try | green after N fixes | FAIL"`.

## Learning loop (how the team gets better across projects)
The team repository improves from what projects learn, and only generic rules ever travel there.

1. **Notice.** Day-to-day notes (`learned` memory entries, audit findings, model outcomes) stay in
   the project. At the close of every turn, ask the tech lead whether any failure or good outcome
   has now been seen at least twice. Those become `LEARN:` proposals.
2. **Propose.** `team.py learn propose --agent <agent | all> --source <who> --evidence <turn>
   --text "<the rule, in generic words>"`. The tool refuses the text if it holds a secret, an email,
   an IP or web address, a local path, the project's own name, or any word in the project's
   `.handoff/private-words.txt`. That file must exist before the first proposal: list the project's
   name, domains, hosts, brand terms and people in it, one per line.
3. **Decide.** Put the rule to the owner as a 1/0 proposal, in its final wording.
   `team.py learn decide <id> 1|0`.
4. **Apply.** `team.py learn apply <id> --repo <clone of the team repository>` checks the text
   again, adds the line to `LEARNED.md` and raises the plugin's patch version, so every project
   receives it on its next `claude plugin update`. Commit those two files on a branch `learn/<id>`
   with a generic message. The owner merges it. Never push it yourself unless the owner says so.
5. **Use.** When you load this skill, read `LEARNED.md` beside it and put the lines for an agent
   (and the `all` lines) into that agent's brief.

Only the rule's one line leaves the project. Its evidence, the turn folder, the memory file and the
proposal log stay in the project's `.handoff/`. Nothing about one project, its owner, its users, its
hosts or its secrets ever goes into the team repository, in a file or in a commit message.

## What stays in the project
Brand and design rules, the stack, hosting and deploy steps, standing owner decisions and the
project's own agents live in the project's CLAUDE.md and folders. Where the project's rules and
this file disagree, the project wins.
