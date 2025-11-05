# Quick Start Guide

Get started with Claude Code Marketplace in 5 minutes!

## What is Claude Code Marketplace?

A collection of professional development workflow plugins for Claude Code that implement intelligent processing loops for audit, research, review, feature development, and bug fixing.

## Installation

```bash
# Clone the marketplace
git clone https://github.com/avovello/claude-code-marketplace.git
```

## Using Plugins

### Option 1: Copy Plugin to Your Project

```bash
# Copy the plugin you want
cp -r claude-code-marketplace/plugins/feature .claude/workflows/

# Use it in Claude Code
# Just describe what you want: "Implement feature X"
# Claude Code will use the feature workflow automatically
```

### Option 2: Reference from Marketplace

```bash
# Create a symlink
ln -s /path/to/claude-code-marketplace/plugins/feature .claude/workflows/feature
```

## Quick Examples

### 1. Audit a Codebase

```
You: "Audit the authentication system and create documentation"

Claude: [Uses Audit Plugin]
1. Explores src/auth/ directory
2. Maps authentication flow
3. Identifies security patterns
4. Creates ARCHITECTURE.md and COMPONENTS.md
```

### 2. Add a Feature

```
You: "Add email validation to the registration form"

Claude: [Uses Feature Plugin]
1. ✅ Creates implementation plan
2. ✅ Reviews plan
3. ✅ Implements feature
4. ✅ Runs tests → Fix bugs → Re-test (loop)
5. ✅ Code review → Apply feedback → Re-review (loop)
6. ✅ Updates documentation
7. ✅ Creates deployment plan
```

### 3. Fix a Bug

```
You: "Fix the bug where login fails with special characters"

Claude: [Uses Bugfix Plugin]
1. ✅ Reproduces bug
2. ✅ Creates failing test
3. ✅ Analyzes root cause
4. ✅ Plans fix
5. ✅ Implements fix → Tests → Re-fix if needed (loop)
6. ✅ Runs regression tests
7. ✅ Updates documentation
```

### 4. Research Technology

```
You: "Compare React, Vue, and Svelte for our dashboard project"

Claude: [Uses Research Plugin]
1. Researches each framework
2. Creates comparison matrix
3. Evaluates for your use case
4. Recommends best fit with reasoning
```

### 5. Code Review

```
You: "Review my changes focusing on security and performance"

Claude: [Uses Review Plugin]
1. Reviews code quality
2. Security review
3. Performance review
4. Consolidates feedback
5. You fix issues → Re-review (loop)
6. Creates review report
```

## Available Plugins

| Plugin | Purpose | Key Feature |
|--------|---------|-------------|
| 🔍 **Audit** | Audit internal codebases | Architecture mapping |
| 📚 **Research** | Research external technologies | Comparative analysis |
| 👁️ **Review** | Code review | Multi-perspective review |
| ✨ **Feature** | Build features | Complete dev lifecycle |
| 🐛 **Bugfix** | Fix bugs | Root cause analysis |

## Processing Loops

All plugins use intelligent loops:

### Testing Loop
```
Test → (Fail?) → Analyze → Fix → Re-test → (Repeat until pass)
```

### Review Loop
```
Review → (Issues?) → Fix → Re-review → (Repeat until approved)
```

### Planning Loop
```
Plan → Review → (Issues?) → Revise → Re-review
```

## Configuration

Create `.claude/marketplace-config.yaml`:

```yaml
# Simple configuration
feature:
  testing:
    max_fix_iterations: 3
  review:
    max_iterations: 2

bugfix:
  fixing:
    max_fix_iterations: 3
```

See `examples/config.example.yaml` for full configuration options.

## Common Workflows

### Full Feature Development
1. **Research** → Compare implementation approaches
2. **Feature** → Implement with quality loops
3. **Review** → Comprehensive review
4. Deploy!

### Legacy Code Modernization
1. **Audit** → Understand existing code
2. **Research** → Research modern alternatives
3. **Feature** → Implement migration
4. **Review** → Ensure quality

### Bug Fix with Analysis
1. **Bugfix** → Fix with root cause analysis
2. **Audit** → Look for similar bugs
3. **Review** → Ensure fix quality

## Hooks (Advanced)

Automate actions at key points:

```yaml
# .claude/hooks/pre-commit.yaml
name: pre-commit
actions:
  - name: run_tests
    command: npm test
    blocking: true
```

See `hooks/*.example.yaml` for hook examples.

## Next Steps

1. **Try a plugin**: Start with Audit or Research
2. **Read examples**: Check `examples/USAGE_GUIDE.md`
3. **Customize**: Copy `config.example.yaml` and customize
4. **Create hooks**: Automate your workflow
5. **Contribute**: Add your own plugins!

## Documentation

- 📖 **Full README**: `/README.md`
- 🎯 **Usage Guide**: `/examples/USAGE_GUIDE.md`
- 🏗️ **Approaches**: `/docs/approaches.md`
- 🤝 **Contributing**: `/CONTRIBUTING.md`

## Support

- **Issues**: https://github.com/avovello/claude-code-marketplace/issues
- **Discussions**: Use GitHub Discussions
- **Examples**: See `examples/` directory

## Key Concepts

### Subagents
Specialized AI agents for specific tasks:
- `tester`: Runs and analyzes tests
- `bug-analyzer`: Identifies root causes
- `documenter`: Creates documentation

### Processing Loops
Intelligent cycles that repeat until quality criteria met:
- **Test Loop**: Test → Fix → Re-test
- **Review Loop**: Review → Fix → Re-review

### Workflows
Orchestrated sequences of steps with loops:
- Feature workflow: Plan → Implement → Test → Review → Document
- Each phase can loop if needed

## Tips

1. **Let loops run**: They improve quality
2. **Customize iterations**: Adjust `max_iterations` for your needs
3. **Use hooks**: Automate repetitive tasks
4. **Start simple**: Try basic features before complex ones

## Example: Complete Feature Development

```bash
# In your project with Claude Code

You: "Add user authentication with OAuth2. Make sure to:
- Plan the implementation
- Write comprehensive tests
- Get code reviewed
- Update documentation"

Claude: [Uses Feature Plugin automatically]

✅ Planning (with review loop)
   → Created plan
   → Plan reviewed and approved

✅ Implementation
   → Implemented OAuth2 integration
   → Added auth middleware
   → Created user model

✅ Testing (with bug-fix loop)
   → Test Run 1: 3 failures
   → Analyzed and fixed bugs
   → Test Run 2: All pass ✅

✅ Code Review (with feedback loop)
   → Review 1: Security concerns raised
   → Applied fixes
   → Review 2: Approved ✅

✅ Documentation
   → Updated README
   → Added API documentation
   → Updated CHANGELOG

✅ Deployment Prep
   → Created deployment checklist
   → Documented environment variables
   → Created rollback plan

Complete! Ready to deploy.
```

That's it! You're ready to use Claude Code Marketplace. 🚀

Start with a simple request and let the plugins handle the complex workflows!
