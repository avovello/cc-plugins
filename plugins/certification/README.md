# Certification Plugin

Claude Certified Architect – Foundations exam preparation with interactive training, mock exam simulation, performance analysis, and content updates.

## Commands

| Command | Description |
|---------|-------------|
| `/training` | Interactive domain study with concepts, misconceptions, and mini-quizzes |
| `/training N` | Study a specific domain (1–5) or task statement (e.g., 1.3) |
| `/quiz` | Full mock exam simulation (30 questions, 4 random scenarios, weighted domains) |
| `/quiz --domain N` | Focus quiz on a specific domain |
| `/quiz --hard --count 10` | Custom difficulty and question count |
| `/analysis` | Performance analysis with domain breakdown, trends, and study recommendations |
| `/analysis --history` | Show all quiz session results |
| `/update` | Check for documentation changes that may affect question accuracy |

## Skills

| Skill | Purpose |
|-------|---------|
| `domain-1-agentic-architecture` | Training curriculum for Domain 1 (27%) |
| `domain-2-tool-design-mcp` | Training curriculum for Domain 2 (18%) |
| `domain-3-claude-code-config` | Training curriculum for Domain 3 (20%) |
| `domain-4-prompt-engineering` | Training curriculum for Domain 4 (20%) |
| `domain-5-context-management` | Training curriculum for Domain 5 (15%) |
| `scoring` | Scoring formula, question selection algorithm, domain weighting |
| `study-path` | Adaptive recommendation engine for targeted study |

## Resources

| Resource | Contents |
|----------|----------|
| `resources/question-bank/` | 173 questions across 5 domains (Easy/Medium/Hard) |
| `resources/exam-guide-reference.md` | Exam format, domains, scenarios, distractor archetypes |

## Exam Overview

- **Format**: Multiple choice (1 correct, 3 distractors)
- **Score**: Scaled 100–1000, passing at 720
- **Scenarios**: 4 of 6 randomly selected per sitting
- **Domains**: Agentic Architecture (27%), Tool Design & MCP (18%), Claude Code Config (20%), Prompt Engineering (20%), Context Management (15%)

## Workflow

```
/training → study weak domains → /quiz → take mock exams → /analysis → identify gaps → repeat
```

1. Start with `/training` to learn domain concepts
2. Take practice exams with `/quiz`
3. Review performance with `/analysis`
4. Focus on weak areas with `/training N` or `/quiz --domain N`
5. Periodically run `/update` to check for content freshness

## Data Persistence

Quiz and training results are stored in `~/.claude/certification/`:
- `results.jsonl` — quiz session results
- `training-progress.jsonl` — training session records
