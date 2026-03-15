# Claude Certified Architect – Foundations Exam Reference

## Format
- Multiple choice: 1 correct, 3 distractors
- Scaled score 100–1000, passing at 720
- 4 of 6 scenarios randomly selected per sitting

## Domains & Weights

| Domain | Weight | Task Statements |
|--------|--------|-----------------|
| 1. Agentic Architecture & Orchestration | 27% | 1.1–1.7 (7) |
| 2. Tool Design & MCP Integration | 18% | 2.1–2.5 (5) |
| 3. Claude Code Configuration & Workflows | 20% | 3.1–3.6 (6) |
| 4. Prompt Engineering & Structured Output | 20% | 4.1–4.6 (6) |
| 5. Context Management & Reliability | 15% | 5.1–5.6 (6) |
| **Total** | **100%** | **30** |

## Scenarios

1. **Customer Support Resolution Agent** — Agent SDK, MCP tools, escalation (Domains 1, 2, 5)
2. **Code Generation with Claude Code** — CLAUDE.md, commands, plan mode (Domains 3, 5)
3. **Multi-Agent Research System** — Coordinator-subagent, context passing (Domains 1, 2, 5)
4. **Developer Productivity with Claude** — Built-in tools, MCP servers (Domains 2, 3, 1)
5. **Claude Code for CI/CD** — -p flag, structured output, review pipelines (Domains 3, 4)
6. **Structured Data Extraction** — JSON schemas, tool_use, batch API (Domains 4, 5)

## Distractor Archetypes
- Over-engineered solution
- Solves the wrong problem
- Probabilistic when deterministic is needed
- Feature/flag that doesn't exist

## Key Recurring Themes
- Programmatic enforcement vs prompt-based guidance
- Context isolation of subagents
- Tool description quality as root cause of selection failures
- When NOT to retry / when retries are futile
- Batch API limitations (no multi-turn tool use, no latency SLA)
