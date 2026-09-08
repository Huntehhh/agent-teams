# Debugging Team Template

**Trigger:** Complex bugs with unclear root cause.

## Base Agents (3)

### Agent: Hypothesis A

```
Investigate [BUG] assuming the root cause is in [AREA_A].
- Read relevant source files
- Trace the execution path
- Check for edge cases and race conditions
- Present evidence for/against this hypothesis
```

### Agent: Hypothesis B

```
Investigate [BUG] assuming the root cause is in [AREA_B].
- Read relevant source files
- Trace the execution path
- Check for configuration issues
- Present evidence for/against this hypothesis
```

### Agent: Environment Inspector

```
Check if [BUG] is caused by environmental factors:
- Dependency versions, breaking changes in recent updates
- OS/platform-specific behavior
- Docker/container configuration
- Network, DNS, or port conflicts
Present findings and recommended diagnostic steps.
```

## Scaling Options

| Need | Add Agent |
|------|-----------|
| Additional hypothesis | Hypothesis C/D/E — one agent per distinct theory |
| Data layer | Data Inspector — query state, check migrations, verify schema |
| Concurrency | Race Condition Analyst — timing, locks, async ordering |
| History | Git Archaeologist — bisect, blame, find the introducing commit |

## Coordination

- Pattern: **Competitive** — each hypothesis agent works independently on the same bug.
- All hypothesis agents present evidence simultaneously.
- If 4+ agents: add a **Triage Lead** that compares evidence strength, proposes next steps.
- Final output: ranked hypotheses with evidence, recommended fix path.
