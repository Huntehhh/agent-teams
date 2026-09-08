# Scaling Heuristics

Rules for proposing agent count beyond what the user requests.

## Core Principle

Propose 1.5-2x the requested agent count with specific role justifications. Always confirm expanded proposal with user before launching.

## By Task Type

### Research Tasks
- +1 **Synthesizer** (always, unless user already specified one)
- +1 **Fact-Checker** when accuracy is critical (medical, legal, financial, security)
- +1 per additional search domain that requires specialized knowledge

**Example:** "3 research agents" -> propose 5:
- 3 Researchers (as requested)
- 1 Synthesizer (cross-references and resolves contradictions)
- 1 Fact-Checker (verifies claims against primary sources)

### Code Review Tasks
- +1 per additional concern area beyond what the user named
- Common implicit concerns: security, performance, test coverage, API design
- If user says "review my PR" with no specifics, propose 3 (security + performance + tests)

**Example:** "2 reviewers for security and performance" -> propose 3:
- 2 Reviewers (as requested: security, performance)
- 1 Test Coverage Reviewer (untested code is a risk amplifier for both concerns)

### Build Tasks
- +1 **Integration Tester** when backend + frontend agents exist
- +1 **Contract Agent** when API boundaries are unclear
- +1 **Documentation Agent** for user-facing features

**Example:** "2 agents to build this feature" -> propose 3:
- 1 Backend (API + data layer)
- 1 Frontend (UI + state)
- 1 Integration Tester (verifies the two halves connect)

### Debug Tasks
- +1 per additional hypothesis area worth investigating
- +1 **Environment Inspector** when symptoms suggest infra/config
- +1 **Git Archaeologist** when the bug is a regression

**Example:** "2 agents to debug this" -> propose 3:
- 2 Hypothesis Investigators (as requested)
- 1 Environment Inspector (rules out infra as root cause)

## General Rules

| Team Size | Add |
|-----------|-----|
| 1-3 agents | No coordinator needed |
| 4-5 agents | Propose a dedicated **Coordinator** role |
| 6+ agents | Coordinator is mandatory — splits into sub-teams |

### Coordinator Role

```
Coordinate team output. Track which agents have reported. Cross-reference
findings for contradictions or gaps. Produce a unified deliverable.
Do not duplicate agent work — synthesize only.
```

## Anti-Patterns

- Do not add agents for marginal concerns. Each agent must have a distinct, non-overlapping scope.
- Do not propose more than 2x the requested count without strong justification.
- Do not add a coordinator for teams of 3 or fewer unless coordination is the primary challenge.
- Do not scale when the task is inherently sequential — more agents do not help pipeline bottlenecks.

## Confirmation Required

Never launch an expanded team without user confirmation. Present the full team plan and wait for approval or modification.
