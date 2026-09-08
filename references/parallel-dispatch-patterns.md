# Parallel Dispatch — Prompt-Writing Patterns

**Load this when:** dispatching multiple agents in parallel via the "Independent" coordination pattern (Section 5 of the parent SKILL.md), especially when you have 2+ independent problems (different test files, different subsystems, different bugs) that can be investigated without shared state.

**Distilled from:** `superpowers/dispatching-parallel-agents` (merged 2026-05-20). Adds prompt-quality checklists, anti-pattern table, and "when NOT to use" guidance that the parent SKILL.md doesn't cover in depth.

## When parallel dispatch is the right move

- **3+ test files failing with different root causes** — sequential investigation wastes time.
- **Multiple subsystems broken independently** — each can be understood without context from others.
- **No shared state between investigations** — agents won't interfere.
- **Each problem can be solved without seeing the others** — true independence.

## When NOT to use parallel dispatch

| Symptom | Why parallel hurts | Do instead |
|---------|--------------------|------------|
| Failures are related (fixing one might fix others) | Wasted agents on already-solved problems | Single agent investigates root cause first |
| Need to understand full system state | Each agent has narrow view, misses the whole | Single agent, full context |
| Exploratory debugging (you don't know what's broken yet) | Can't define focused agent scopes | Single agent explores, then dispatch if needed |
| Agents would interfere (editing same files, shared resources) | Conflicts, lost work | Sequential agents OR single agent |

## Anatomy of a focused agent prompt

Every parallel-dispatched agent gets four things:

1. **Specific scope** — One test file, one subsystem, one bug. Not "fix the tests."
2. **Clear goal** — "Make these N tests pass" or "Fix the race condition in X." Concrete, verifiable.
3. **Constraints** — "Do NOT change production code." Or "Don't modify other tests." Prevents scope creep.
4. **Expected output format** — "Return summary of root cause + changes made + remaining concerns." Predictable structure for integration.

## Common mistakes table

| ❌ Bad prompt | ✅ Good prompt | Why it matters |
|---------------|----------------|----------------|
| "Fix all the tests" | "Fix `agent-tool-abort.test.ts` failures only" | Vague scope → agent wanders |
| "Fix the race condition" | "3 tests fail in `agent-tool-abort.test.ts`: [paste names + error messages]. These are timing issues." | No context → agent guesses |
| "Investigate the bug" | "Investigate why `pendingToolCount` returns 0 instead of 3 in test X. Likely an async timing bug." | No hypothesis → agent has nothing to test |
| "Make it work" | "Replace arbitrary timeouts with event-based waiting. Don't just increase timeouts — find the real issue." | No constraint → agent picks the easy path |
| "Tell me what you find" | "Return: (1) root cause in one sentence, (2) files changed with line numbers, (3) any remaining concerns" | Unstructured output → integration is harder |

## Prompt template

```markdown
Fix the [N] failing tests in [PATH/TO/TEST_FILE.test.ts]:

[Paste failing test names + error messages]

These are [hypothesis: timing issues / API contract violation / etc.].

Your task:
1. Read the test file and understand what each test verifies.
2. Identify root cause — timing issues, actual bugs, or stale assertions?
3. Fix by:
   - [Specific approach 1, e.g., "Replace timeouts with event-based waiting"]
   - [Specific approach 2, e.g., "Fix bugs in abort implementation if found"]
   - [Specific approach 3, e.g., "Adjust test expectations if testing changed behavior"]

Constraints:
- Do NOT [thing to avoid, e.g., "increase timeouts as a shortcut"]
- Do NOT [other thing to avoid, e.g., "change production code"]
- Focus only on [this file / this subsystem]

Return:
- One-sentence root cause
- List of files changed with line numbers
- Any remaining concerns or related issues you noticed
```

## Worked example

**Scenario:** 6 test failures across 3 files after a major refactor.

**Failures:**
- `agent-tool-abort.test.ts`: 3 failures (timing-suspect)
- `batch-completion-behavior.test.ts`: 2 failures (tools not executing)
- `tool-approval-race-conditions.test.ts`: 1 failure (execution count = 0)

**Independence check:** Abort logic is separate from batch completion is separate from race conditions. → True independence.

**Dispatch (3 agents in parallel):**

```
Agent 1 → Fix agent-tool-abort.test.ts (timing issues)
Agent 2 → Fix batch-completion-behavior.test.ts (event structure)
Agent 3 → Fix tool-approval-race-conditions.test.ts (async waiting)
```

**Results (collected after fan-in):**
- Agent 1: Replaced timeouts with event-based waiting
- Agent 2: Fixed event structure bug (threadId in wrong place)
- Agent 3: Added wait for async tool execution to complete

**Integration:** All fixes independent, no conflicts, full suite green.

**Time saved:** 3 problems solved in time of 1.

## Verification after fan-in

When all agents return, before declaring done:

1. **Read each summary** — understand what changed. Don't skip this.
2. **Check for conflicts** — did agents edit the same files? If yes, manual reconciliation.
3. **Run full test suite** — verify all fixes work together, not just in isolation.
4. **Spot check** — agents can make systematic errors (e.g., all three used the same anti-pattern). Look for repeated mistakes.

## Key benefits

- **Parallelization** — N problems solved in time of 1.
- **Focus** — each agent has narrow scope, less context to track, less risk of going off the rails.
- **Independence** — agents don't interfere with each other or your main context.
- **Speed** — wall time scales sub-linearly with problem count.
