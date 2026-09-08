# Merge Provenance — superpowers/dispatching-parallel-agents → agent-teams/references/parallel-dispatch-patterns.md

**Source path**: `S:\OPEN-SOURCE-REPOSITORIES\superpowers\skills\dispatching-parallel-agents\` (Hunter had byte-identical accidental copy at `~/.claude/skills/dispatching-parallel-agents/` — deleted 2026-05-20).
**Merge date**: 2026-05-20
**Merger**: Claude Opus 4.7 (this session)
**Status**: Distilled into `references/parallel-dispatch-patterns.md`. Hunter's local copy deleted.

## What was extracted

The superpowers SKILL.md was 183 lines. Hunter's `agent-teams/SKILL.md` already covers the broader coordination patterns (including "Independent"). What `dispatching-parallel-agents` added that agent-teams didn't:

| New value | Where it landed |
|-----------|-----------------|
| Prompt-quality checklist (4 elements: scope, goal, constraints, output format) | `references/parallel-dispatch-patterns.md` — "Anatomy of a focused agent prompt" section |
| Common Mistakes table (vague prompts, no context, no constraints, vague output) | Same file — "Common mistakes table" section |
| Explicit "When NOT to use" rules (related failures, exploratory debugging, shared state) | Same file — "When NOT to use parallel dispatch" section |
| Prompt template with placeholders | Same file — "Prompt template" section |
| Worked example (6 failures across 3 files) | Same file — "Worked example" section |
| Verification protocol after fan-in (read summaries, check conflicts, full suite, spot check) | Same file — "Verification after fan-in" section |

## What was added to agent-teams/SKILL.md

One line under the "Independent" coordination pattern (Section 5):

> **For prompt-writing best practices when dispatching independent agents in parallel** (focused-scope checklist, anti-patterns, "when NOT to use" guidance, worked example): see [references/parallel-dispatch-patterns.md](references/parallel-dispatch-patterns.md).

This is the cross-reference that makes the new doc discoverable. The parent SKILL.md body remains lean.

## What was REJECTED

- The Graphviz flowchart (DOT format) — Hunter doesn't currently use Graphviz in any of his skills. Captured the same decision logic in prose form via the "When parallel dispatch is the right move" + "When NOT to use" sections.
- "Real-World Impact" timestamped story ("From debugging session 2025-10-03") — Per `tdd-for-skills.md` anti-pattern guidance: avoid narrative storytelling in skills. The same example IS preserved under "Worked example" but without the date/personal-story framing.

## Verification

Run `~/.claude/skills/skill-create-and-ship/scripts/quick_validate.py ~/.claude/skills/agent-teams/` to confirm validation. (Note: validator may complain about cross-refs to scaling-heuristics.md / templates/ — those are pre-existing and not part of this merge.)

## Rollback procedure

If Hunter wants to revert:
1. The superpowers source at `S:\OPEN-SOURCE-REPOSITORIES\superpowers\skills\dispatching-parallel-agents\` is unchanged.
2. Delete `~/.claude/skills/agent-teams/references/parallel-dispatch-patterns.md`.
3. Remove the cross-ref line added to `agent-teams/SKILL.md` Section 5.
4. (Optional) Restore Hunter's local copy of the standalone skill if desired.
