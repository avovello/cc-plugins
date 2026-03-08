# Feature Development Plugin

Systematic feature development with codebase exploration, architecture design, and team-based implementation loops.

> **Requires**: `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`

## Installation

```bash
/plugin install https://github.com/avovello/cc-plugins.git#plugins/feature-development
```

## Usage

```bash
/feature "Add real-time notifications with WebSockets"
```

## Agents (1)

| Agent | Purpose | Tools |
|-------|---------|-------|
| `code-architect` | Architecture design reference — applies SOLID, Clean Architecture, Design Patterns, DDD | Read, Glob, Grep, Bash |

The `code-architect` agent's role is now filled by the architect teammate in the Phase 4 do-loop. The agent file serves as role context.

## Skills (7 local + 1 from shared plugin)

| Skill | Used In | Purpose |
|-------|---------|---------|
| `clarifying-questions` | Phase 3 | Structured ambiguity resolution |
| `solid-principles` | Phase 4 | SRP, OCP, LSP, ISP, DIP |
| `clean-architecture` | Phase 4 | Layered structure with Dependency Rule |
| `design-patterns` | Phase 4 | Creational, Structural, Behavioral patterns |
| `domain-driven-design` | Phase 4 | Bounded Contexts, Aggregates, Domain Events |
| `review-loop` | Phase 5 | Code review guidelines and quality criteria |
| `testing-loop` | Phase 6 | Test writing guidelines and coverage strategy |
| `do-loop` (shared) | Phase 4, 5, 6 | Iteration protocol for doer+critic agent team loops |

## Workflow

```
Discovery → Exploration → Questions → Arch Loop → Implement Loop → Test Loop → Summary
```

### Phases 1-3: Understanding (no teams)

1. **Discovery** — gather requirements, confirm with user
2. **Exploration** — parallel Explore subagents scan the codebase
3. **Questions** — resolve ALL ambiguities (hard stop until answered)

### Phases 4-6: Three sequential do-loops (agent teams)

4. **Architecture loop** — architect + arch-reviewer iterate on blueprint design (max 3 rounds). User confirms blueprint before proceeding.
5. **Implementation loop** — implementer + code-reviewer iterate on code (max 3 rounds). Blueprint is source of truth.
6. **Test loop** — test-writer + test-reviewer iterate on tests (1-2 rounds). Target 80%+ coverage.

### Phase 7: Summary

Summarize components built, key decisions, files changed, next steps.

## Design Decisions

- **3 sequential do-loops, not 1 big team**: Each loop has a focused scope and a clean handoff. Architecture must be approved before implementation starts.
- **Architect teammate, not code-architect subagent**: Agent teams allow the arch-reviewer to challenge the architect in real-time, not review a finished artifact.
- **testing-loop and review-loop skills are now wired**: Previously orphaned skills now provide domain context for code-reviewer and test-writer teammates.
- **Depends on shared plugin**: Uses the `do-loop` skill for team iteration protocol.

## Dependencies

- **shared plugin**: provides `do-loop` skill

## Related Plugins

- **QA Plugin**: `/qa` command for additional test scenarios beyond the feature scope
- **Shared Plugin**: `do-loop` iteration protocol

## Version
2.1.0
