# Quick Start Guide

Get started with CC Plugins in 5 minutes.

## What is CC Plugins?

A collection of 6 plugins for Claude Code with 7 slash commands, 10 specialized agents, and 19 skills. Covers feature development, code review, bug fixing, quality assurance, and certification exam preparation.

## Prerequisites

Install Claude Code:

```bash
npm install -g @anthropic-ai/claude-code
```

For bugfix, feature-development, and QA plugins (agent teams):

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

## Installation

### Quick Install (Recommended)

```bash
# Navigate to your project
cd your-project

# Start Claude Code
claude

# Install plugins
/plugin install https://github.com/avovello/cc-plugins.git#plugins/shared
/plugin install https://github.com/avovello/cc-plugins.git#plugins/review
/plugin install https://github.com/avovello/cc-plugins.git#plugins/bugfix
/plugin install https://github.com/avovello/cc-plugins.git#plugins/qa
/plugin install https://github.com/avovello/cc-plugins.git#plugins/feature-development
/plugin install https://github.com/avovello/cc-plugins.git#plugins/certification
```

### Manual Install

```bash
# Clone the repo
git clone https://github.com/avovello/cc-plugins.git

# Copy specific plugins to your project
cp -r cc-plugins/plugins/review .claude/plugins/
cp -r cc-plugins/plugins/bugfix .claude/plugins/

# Or copy all plugins
cp -r cc-plugins/plugins/* .claude/plugins/
```

## How to Use Plugins

All plugins are invoked using **slash commands** within Claude Code:

```
/command-name [arguments]
```

## Quick Examples

### 1. Review Code Changes

```bash
/review                        # Review uncommitted changes (default)
/review --commit abc123        # Review a specific commit
/review --commit HEAD~3..HEAD  # Review a commit range
/review --pr 123               # Review a pull request
```

Launches an investigator to map changes, then 4 specialized reviewers (security, architecture, performance, bugs) analyze in parallel. Reports only high-confidence findings.

### 2. Fix a Bug

```bash
/bugfix "Login fails when email contains special characters"
```

Workflow: Investigate (subagents) → Fix+Review (team loop) → Test+Review (team loop) → Verify

### 3. Add a Feature

```bash
/feature "Add OAuth2 authentication with Google"
```

Workflow: Discovery → Exploration → Questions → Architecture Loop → Implementation Loop → Test Loop → Summary

### 4. Run Tests

```bash
/qa                                    # Run all tests
/qa run unit tests                     # Run unit tests
/qa write tests for src/services/auth  # Write tests with review
/qa test login flow in browser         # Browser testing
```

## Available Plugins

| Plugin | Command | Agents | Purpose |
|--------|---------|--------|---------|
| **Shared** | — | 0 | Do-loop skill for agent team iteration protocol |
| **Review** | `/review` | 7 | Two-phase code review: investigate then review in parallel |
| **Bugfix** | `/bugfix` | 2 | Investigation (subagents) + fix+review (team loops) |
| **Feature** | `/feature` | 1 | 3 team do-loops: architecture, implementation, tests |
| **QA** | `/qa` | 0 | Test writing, execution, browser testing (do-loop agent teams) |
| **Certification** | `/training`, `/quiz`, `/analysis`, `/update` | 0 | Exam prep: training, mock exams, performance analysis |

## Common Workflows

### Pre-Commit Review

```bash
# 1. Write your code changes
# 2. Review before committing
/review

# 3. Fix any issues found
# 4. Review again
/review
```

### Bug Fix Workflow

```bash
# 1. Investigate and fix the bug
/bugfix "Description of the issue"

# 2. Review the fix
/review

# 3. Run tests
/qa
```

### Feature Development

```bash
# 1. Develop the feature
/feature "Add user authentication"

# 2. Review the implementation
/review

# 3. Run tests
/qa
```

### 5. Prepare for Certification

```bash
/training              # Study recommended domain (or start with Domain 1)
/training 1            # Study Domain 1: Agentic Architecture
/quiz                  # Take a full mock exam (30 questions)
/quiz --domain 2       # Focus quiz on Domain 2
/analysis              # Review performance and get study recommendations
/update                # Check for documentation changes
```

Workflow: Train domains → Take mock exams → Analyze gaps → Focus weak areas → Repeat

## Tips

1. **Start with /review** — it's the fastest way to catch issues before committing
2. **Combine plugins** — use `/bugfix` then `/review` then `/qa` for a complete fix workflow
3. **Check plugin READMEs** — each plugin has detailed documentation in `plugins/*/README.md`

## Documentation

- **README.md** — Complete overview of all plugins
- **Plugin READMEs** — Detailed docs in each `plugins/*/README.md`
- **Command Files** — See `plugins/*/commands/*.md` for workflows
- **Agent Files** — See `plugins/*/agents/*.md` for agent specifications
- **CLAUDE.md** — Project conventions and architecture decisions

## Support

- **Repository**: https://github.com/avovello/cc-plugins
- **Issues**: Report problems on GitHub Issues
- **Contributing**: See CONTRIBUTING.md
