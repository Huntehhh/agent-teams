# agent-teams

A Claude Code skill for composing and coordinating multi-agent teams on demand — no fixed team sizes, no boilerplate to copy-paste every time you want to fan work out across several agents. It's pure prompting doctrine: a decision framework and a set of role templates, not a service or a script you run.

## The problem this solves

"Spin up a few agents to research this" is easy to say and easy to get wrong in practice — teams end up too small to cover the ground, or too large with agents stepping on each other's territory, or missing an obvious role (nobody synthesizes the research; nobody checks security on the code review). This skill is a checklist and a set of patterns for getting the team shape right before you launch anything, then coordinating cleanly once it's running.

## What it actually gives you

- **A scaling heuristic.** When you ask for N agents, it evaluates whether the task needs supporting roles you didn't ask for — a synthesizer for research, a security reviewer for code review, an integration tester for a build task — and proposes 1.5–2x with a one-line justification per addition, capped unless you explicitly want more.
- **Five coordination patterns**, matched to task shape: fan-out/fan-in (parallel research → one synthesizer), pipeline (sequential, each stage's output feeds the next), independent (parallel, no cross-dependencies), competitive (multiple agents solve the same problem, compare results), and hybrids of these.
- **A confirmation flow.** The team plan is always presented before anything launches — roles, skill assignments, coordination pattern, dependencies — so you can drop a role, add one, or scale back before agents start burning tokens.
- **Prompt-quality guidance for dispatching parallel agents** (`references/parallel-dispatch-patterns.md`): what a focused agent prompt needs (scope, goal, constraints, output format), and a table of the mistakes that produce shallow or overlapping work — vague prompts, missing context, no stated constraints, no output format.

## What it is not

There's no code here to run. It's a set of markdown files that shape how an AI orchestrates other AI agents through your existing tool-calling setup (Claude Code's Task/Agent tool, or your own equivalent). If you're looking for a multi-agent *runtime*, this isn't that — it's the judgment layer that sits above one.

## Requirements

Just a Claude Code (or similar) session with the ability to spawn sub-agents. No API keys, no external services, no scripts to install.

## Install

```bash
git clone https://github.com/Huntehhh/agent-teams.git ~/.claude/skills/agent-teams
```

Trigger it by describing the task ("research React frameworks", "review this PR", "spin up a team to debug this") or naming it directly ("use 5 agents to...").

## Layout

- `SKILL.md` — the routing logic: input parsing, scaling heuristics, coordination pattern selection, confirmation flow
- `references/scaling-heuristics.md` — the detailed rules for when and how much to scale a team
- `references/parallel-dispatch-patterns.md` — how to write a prompt for an independently-dispatched agent, with a worked example and a common-mistakes table
- `references/templates/` — starting points for the four common task shapes (code review, research, debugging, build)
