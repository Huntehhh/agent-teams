# Research Team Template

**Trigger:** Deep research tasks requiring multiple search strategies.

**Exa is mandatory.** Run the Exa gate (`bash ~/.claude/skills/exa-search/scripts/exa_doctor.sh`) before launch, and append the Exa rule from SKILL.md Section 4 ("Web research runs on Exa") verbatim to both searcher prompts and to the Fact-Checker if you add one.

## Base Agents (3)

### Agent: Broad Searcher

```
Search for [TOPIC] using the deep-research skill: read its SKILL.md first and
follow its Solo pipeline, Exa included. Focus on:
- Official documentation and announcements
- Major tech publications (TechCrunch, Verge, Ars)
- GitHub repos and discussions
Compile a summary of top 10 most relevant sources with key takeaways.
```

### Agent: Deep Searcher

```
Search for [TOPIC] using Exa semantic search (exa-search scripts) and niche
sources, per the deep-research skill. Focus on:
- Reddit, Hacker News, Lobsters discussions
- Substacks, personal blogs, indie dev posts
- Academic papers, research reports
- Real user experiences and gotchas
Compile a summary of non-obvious insights that broad search would miss.
```

### Agent: Synthesizer

```
Wait for Agent 1 and Agent 2 results. Then:
- Cross-reference findings, resolve contradictions
- Rank sources by credibility and recency
- Identify consensus views vs. contrarian takes
- Produce a structured brief with: Summary, Key Findings, Open Questions, Recommendations
```

## Scaling Options

| Need | Add Agent |
|------|-----------|
| Accuracy | Fact-Checker — verify claims, check dates, cross-reference primary sources |
| Competitive intel | Competitor Analyst — pricing, positioning, feature comparison |
| Technical depth | Code Auditor — read repos, benchmark, test claims against reality |
| Trend analysis | Trend Tracker — historical context, trajectory, emerging signals |

## Coordination

- Pattern: **Fan-out/Fan-in** — searchers fan out, synthesizer fans in.
- Synthesizer must wait for all searchers to complete before starting.
- Fact-checker (if present) runs after synthesizer, flags unverified claims.
- A searcher returning `EXA_FAILURE:` stops the team (SKILL.md Section 4) — the synthesizer never runs on partial input.
- Final output: structured brief with confidence levels per finding.
