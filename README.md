# startup-team

A small expert team for Claude Code, packaged as a plugin. It gives any project the same way of
working: a tech lead triages every task, a designer gets the owner's approval before any UI is
built, an architect plans backend pieces, an executor builds one piece at a time, an auditor checks
the result against the owner's own words, and devops proposes anything that leaves the machine.

The team is project-agnostic. Everything specific to a project (brand, stack, hosting, standing
decisions) stays in that project's `CLAUDE.md`, and the project's rules always win.

## Install

```bash
claude plugin marketplace add mypanda7/startup-team
```

```bash
claude plugin install startup-team@startup-team --scope local
```

Update later with:

```bash
claude plugin update startup-team@startup-team
```

## What is in it

| Part | What it does |
|---|---|
| `agents/tech-lead` | First stop. Splits a task into pieces, lists the open questions, names the route and model per piece. |
| `agents/designer` | PROPOSE, BRIEF and REVIEW modes. No UI is built before the owner's 1. |
| `agents/architect` | Plans API, data model, auth and job pieces before the build. |
| `agents/executor` | Builds one piece from a brief. One local commit. Never pushes. |
| `agents/auditor` | PASS or FAIL against the owner's request, with evidence it re-ran itself. |
| `agents/devops` | Read-only analysis, and proposals for every push, deploy or server change. |
| `agents/security` | Security analysis of the owner's own project. Findings only, no fixes. |
| `agents/skill-sub` | A read-only helper that answers one narrow question for another agent. |
| `skills/team-workflow` | The chain for the main session, plus `scripts/team.py`. |
| `commands/` | `/startup-team:triage`, `:design`, `:audit`, `:learn`. |
| `hooks/` | The team gate: on every request of 8 words or more it reminds the session to triage with the tech lead first. |

### The team gate

The gate only adds a reminder; it never blocks a prompt. It stays quiet for slash commands, pasted
terminal output, short replies, and any request that starts with `quick:`. Set
`STARTUP_TEAM_GATE_OFF=1` to turn it off for a session. It needs Python on the PATH; without one it
does nothing.

## How a task runs

1. A turn folder is created for the task in the project's git-ignored `.handoff/`.
2. The tech lead returns a decision block: pieces that are clear now, questions, blocked pieces.
3. Questions go to the owner one at a time, with options and a recommendation. An answer is final.
4. UI pieces get a design proposal and the owner's 1. Backend pieces get a plan.
5. The executor builds each piece and commits it locally.
6. The auditor checks the whole turn. Fixes go back to the tech lead.
7. Devops proposes the push or deploy. The owner approves it.

## Shared memory

`team.py memory` keeps one append-only file per project (`.handoff/memory/agent-memory.jsonl`) with
owner answers, facts, open items, pointers and learned notes. Its digest goes into every brief.
Text that looks like a secret is refused.

## How the team learns

Day-to-day notes stay in each project. A rule worth sharing is proposed in generic words
(`team.py learn propose`), the owner answers 1 or 0, and an approved rule is added to
[LEARNED.md](LEARNED.md) on a branch that the owner merges. Every project picks it up on its next
plugin update.

This repository is public and shared across projects. Nothing about a single project, its owner,
its users, its hosts or its secrets belongs here. The tool refuses proposals that contain an email
address, an IP address, a key, or any word listed in the project's `.handoff/private-words.txt`.

## Requirements

Claude Code, git, and Python 3.9 or newer for `team.py`.
