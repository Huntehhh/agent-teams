# Build Team Template

**Trigger:** Multi-file feature implementation.

## Base Agents (3)

### Agent: Backend

```
Implement the backend portion of [FEATURE]:
- API routes, handlers, validation
- Database schema changes
- Business logic
Follow existing patterns in the codebase. Write tests.
```

### Agent: Frontend

```
Implement the frontend portion of [FEATURE]:
- UI components, forms, state management
- API client calls
- Error handling and loading states
Follow existing component patterns. Match design system.
```

### Agent: Integration Tests

```
Write integration tests for [FEATURE]:
- End-to-end happy path
- Error scenarios
- Edge cases identified during implementation
Coordinate with backend and frontend agents on API contracts.
```

## Scaling Options

| Need | Add Agent |
|------|-----------|
| API contract | Contract Agent — define types/interfaces first, share with backend + frontend |
| Documentation | Doc Agent — update README, API docs, changelog |
| Migration safety | Migration Agent — write and test DB migrations, rollback plan |
| CI/CD | Pipeline Agent — update build config, add deploy steps |

## Coordination

- Pattern: **Pipeline** — contract first (if present) -> backend + frontend in parallel -> integration tests.
- Backend and frontend agents must agree on API contract before building.
- Integration test agent waits for both to complete.
- If 5+ agents: add a **Build Coordinator** that tracks progress and resolves blocking issues.
- Final output: working feature with tests passing.
