---
name: agent-teams
description: >
  Spawn and coordinate dynamic multi-agent teams scaled to any task complexity. Use when parallel work, fan-out/fan-in research, or multi-role collaboration would outperform a single agent. Capabilities: auto-compose team from task description, explicit role assignment, dynamic scaling, skill injection per agent, user plan confirmation before launch. Coordination patterns: fan-out/fan-in, pipeline, independent, competitive, hybrid. Auto-detects task type (research, review, build, debug, content, migration) and proposes 1.5-2x scaling. Not for single-query lookups — use exa-search or deep-research. Triggers on (non-exhaustive): "use multiple agents", "spin up a team", "fan out", "parallel agents", "swarm this", "multi-agent", "team of agents", "split this across agents".
---

# Agent Teams — Dynamic Multi-Agent Orchestration

Compose, scale, and coordinate agent teams based on user input. No fixed team sizes. Parse intent, propose a team plan, confirm with user, then launch.

---

## 1. Input Parsing

Accept any of these input forms and extract: **role count**, **role descriptions**, and **task context**.

### Natural Language with Counts

```
"2 deep-research + 2 explore agents"
→ Roles: 2x deep-researcher, 2x explorer
```

### Role Descriptions

```
"an agent for security, one for performance"
→ Roles: 1x security-reviewer, 1x performance-reviewer
```

### Task Descriptions (Auto-Compose)

```
"research React frameworks"
→ Auto-suggest: 2x researcher, 1x synthesizer (fan-out/fan-in)

"debug this auth bug"
→ Auto-suggest: 2x hypothesis-investigator, 1x environment-inspector

"review this PR"
→ Auto-suggest: 1x security, 1x performance, 1x test-coverage

"build a dashboard feature"
→ Auto-suggest: 1x backend, 1x frontend, 1x integration-tester
```

### Explicit Agent Count

```
"use 5 agents"
→ Determine roles from task context. If context is ambiguous, ask.
```

When input is ambiguous (e.g., "use agents to help"), ask one clarifying question about the task before proposing a team.

---

## 2. Dynamic Scaling

When the user requests N agents, evaluate whether additional supporting roles improve the outcome. Propose 1.5-2x with specific justifications. See `references/scaling-heuristics.md` for detailed rules.

### Scaling Decision Tree

```
1. Parse requested agent count (N) and roles
2. Identify task type (research | review | build | debug | custom)
3. Check for missing critical roles:
   - Research: synthesizer? fact-checker?
   - Review: test coverage? security?
   - Build: integration tester? contract agent?
   - Debug: environment inspector?
4. Propose additions with one-line justification per added role
5. Cap at 2x unless user explicitly wants more
6. Teams of 4+: propose a coordinator role
```

### Scaling Example

User says: "3 research agents on competitor pricing"

Propose:
```
Team Plan (5 agents):
- Agent 1: Broad Researcher — deep-research — Official docs, major publications, pricing pages
- Agent 2: Deep Researcher — deep-research — Reddit, forums, insider blogs, real user experiences
- Agent 3: Market Researcher — deep-research — Competitor positioning, feature comparison, tier analysis
- Agent 4: Synthesizer — none — Cross-reference all findings, resolve contradictions, rank by confidence
- Agent 5: Fact-Checker — web-search — Verify pricing claims, check dates, flag stale data
Why 5: Research without synthesis produces raw data, not insight. Fact-checking prevents acting on outdated pricing.
```

Wait for user confirmation.

---

## 3. Confirmation Flow

Present the full team plan before launching any agents. Format:

```
Team Plan:
- Agent 1: [Role] — [Skill assignment] — [One-line task description]
- Agent 2: [Role] — [Skill assignment] — [One-line task description]
- Agent 3: [Role] — [Skill assignment] — [One-line task description]

Coordination: [Pattern name — fan-out/fan-in, pipeline, independent, competitive]
Dependencies: [e.g., "Agent 3 waits for 1+2" or "None — all parallel"]
Estimated parallel time: [X turns]
```

### User Responses

- **"go" / "launch" / "yes"** — Launch as proposed.
- **Modification** (e.g., "drop the fact-checker", "add a doc agent") — Adjust plan, re-present, confirm again.
- **"fewer agents"** — Scale back to user's original count, note what coverage is lost.

Never launch without confirmation.

---

## 4. Skill Integration

Assign skills to agents when a skill matches their role. Common mappings:

| Role | Skill | When |
|------|-------|------|
| Researcher / Searcher | `deep-research` | Any research or information-gathering task |
| Explorer | `file-search` | Broad discovery, unknown territory |
| Code reviewer | (none — uses codebase tools) | Code analysis, PR review |
| Builder | (none — uses codebase tools) | Implementation tasks |
| Debugger | (none — uses codebase tools) | Bug investigation |

### Skill Assignment Rules

- If user explicitly assigns a skill to a role, use it exactly as specified.
- If user names a task without specifying skills, auto-map from the table above.
- Agents without a skill assignment use standard Claude Code capabilities.
- One agent can use multiple skills if the role requires it.

### Agent Prompt Construction

Each agent receives a prompt constructed from:

1. **Role definition** — what this agent is responsible for
2. **Skill reference** — link to the skill's SKILL.md if assigned
3. **Task specifics** — the concrete work to do (from user input)
4. **Coordination instructions** — who to wait for, what to produce, where to report
5. **Scope boundaries** — what NOT to do (prevent overlap with other agents)

---

## 5. Coordination Patterns

Select the coordination pattern that fits the task. Specify in the team plan.

### Fan-out / Fan-in

Multiple agents work in parallel on different aspects. One agent collects and synthesizes results.

```
Agents 1-N: Work in parallel on distinct sub-tasks
Agent N+1 (Synthesizer): Wait for all, then combine

Use for: Research, competitive analysis, multi-source data gathering
```

### Pipeline

Agents work sequentially. Each agent's output feeds the next.

```
Agent 1: Produce artifact A
Agent 2: Consume A, produce artifact B
Agent 3: Consume B, produce final output

Use for: Build workflows (contract → implementation → tests),
         content pipelines (research → draft → edit)
```

### Independent

All agents work in parallel on separate, non-overlapping tasks. Results collected at the end.

```
All agents: Work independently, no cross-dependencies
Collection: Aggregate outputs when all complete

Use for: Code review (each reviewer checks different concerns),
         multi-file changes across unrelated modules
```

**For prompt-writing best practices when dispatching independent agents in parallel** (focused-scope checklist, anti-patterns, "when NOT to use" guidance, worked example): see [references/parallel-dispatch-patterns.md](references/parallel-dispatch-patterns.md).

### Competitive

Multiple agents solve the same problem independently. Compare results to find the best solution.

```
Agents 1-N: Each solves the same problem with a different approach
Evaluation: Compare solutions on criteria (correctness, performance, clarity)

Use for: Debugging (multiple hypotheses), algorithm selection,
         architecture decisions
```

### Hybrid

Combine patterns. Common hybrids:

- **Fan-out → Pipeline:** Research in parallel, then sequentially refine
- **Competitive → Fan-in:** Multiple approaches, synthesizer picks the best
- **Pipeline with parallel stages:** Stage 2 has 3 agents working in parallel

---

## 6. Agent Role Construction

When building agent roles from scratch (not from templates), follow this structure:

### Role Prompt Template

```
You are a [ROLE_NAME]. Your responsibility: [ONE_SENTENCE_SCOPE].

Task: [SPECIFIC_WORK_FROM_USER_INPUT]

Approach:
- [Step 1]
- [Step 2]
- [Step 3]

Output format: [WHAT_TO_PRODUCE — findings list, code, report, etc.]

Boundaries:
- Do not [OVERLAP_WITH_OTHER_AGENT_SCOPE]
- Focus only on [THIS_AGENT'S_DOMAIN]
```

### Role Naming Conventions

Use descriptive, specific role names. Avoid generic labels.

| Avoid | Prefer |
|-------|--------|
| Agent 1 | Security Reviewer |
| Researcher | Pricing Researcher |
| Helper | Integration Tester |
| Worker | Schema Migration Agent |

---

## 7. Auto-Composition Rules

When the user provides a task description without specifying agents, auto-compose a team using these rules.

### Task Type Detection

| Signal | Task Type | Default Team |
|--------|-----------|--------------|
| "research", "find out", "compare", "analyze" | Research | 2 researchers + 1 synthesizer |
| "review", "audit", "check", "PR" | Code Review | security + performance + test coverage |
| "build", "implement", "create", "add feature" | Build | backend + frontend + integration tester |
| "debug", "fix", "broken", "not working" | Debug | 2 hypothesis agents + 1 environment inspector |
| "write", "draft", "document" | Content | drafter + editor + fact-checker |
| "migrate", "refactor", "upgrade" | Migration | analyst + implementer + validator |

### Ambiguous Input

If the task does not match any type clearly, ask:
```
What kind of work is this? Examples:
- Research / analysis
- Code review
- Building a feature
- Debugging
- Something else: [describe]
```

One question only. Do not interrogate.

---

## 8. Template References

Use these as starting points when the task matches a known pattern. Customize roles and count based on user input — do not use templates rigidly.

| Template | File | Base Size |
|----------|------|-----------|
| Code Review | `references/templates/code-review.md` | 3 agents |
| Research | `references/templates/research.md` | 3 agents |
| Debugging | `references/templates/debug.md` | 3 agents |
| Build | `references/templates/build.md` | 3 agents |

Templates contain:
- Base agent definitions with prompt text
- Scaling options (which roles to add and when)
- Coordination pattern recommendation

Scaling heuristics: `references/scaling-heuristics.md`

---

## 9. Execution Checklist

Run through this before launching each team:

1. **Parsed input** — extracted role count, descriptions, task context
2. **Scaled** — proposed additional roles with justifications (see heuristics)
3. **Confirmed** — user approved the team plan
4. **Skills assigned** — each agent has skill mappings (or explicit "none")
5. **Coordination set** — pattern selected, dependencies documented
6. **Prompts built** — each agent has role + task + scope boundaries + output format
7. **Launched** — agents started per coordination pattern

---

## 10. Post-Launch Coordination

### Progress Tracking

For teams of 4+ agents, the coordinator (or the orchestrating process) tracks:
- Which agents have reported
- Whether outputs meet expected format
- Cross-agent contradictions or gaps

### Conflict Resolution

When agents produce contradictory outputs:
- Flag the contradiction explicitly
- Present both positions with supporting evidence
- Let the user decide, or defer to the agent with stronger evidence

### Final Deliverable

Combine agent outputs into a single deliverable:
- Structured by topic/concern, not by agent
- Deduplicated — remove redundant findings
- Prioritized — most important findings first
- Actionable — each finding has a clear next step
