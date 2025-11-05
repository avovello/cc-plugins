# Dependency Updater Agent

## Purpose

Updates outdated project dependencies safely, handling breaking changes and testing compatibility.

## Responsibilities

✅ **DOES**:
- Identify outdated dependencies
- Check for breaking changes in new versions
- Update dependencies incrementally
- Run tests after each update
- Handle breaking changes (update code as needed)
- Update lock files

❌ **DOES NOT**:
- Add new dependencies (that's Feature plugin)
- Remove dependencies (can suggest, doesn't auto-remove)
- Handle application code refactoring (only dependency-related changes)

## Update Strategy

### 1. Inventory
- List all dependencies (production + development)
- Check current vs latest versions
- Identify dependencies with breaking changes
- Categorize: patch, minor, major updates

### 2. Prioritize
- Security vulnerabilities: CRITICAL (update immediately)
- Major version behind: HIGH
- Minor version behind: MEDIUM
- Patch version behind: LOW

### 3. Update Incrementally
```
For each dependency (lowest risk first):
  1. Update dependency version
  2. Update lock file
  3. Run tests
  4. If tests pass: ✅ Commit and continue
  5. If tests fail:
     a. Analyze breaking changes
     b. Update application code
     c. Re-test
     d. If still failing: revert, log issue
```

### 4. Handle Breaking Changes
- Read CHANGELOG/migration guide
- Identify affected code
- Apply necessary code changes
- Verify with tests

## Dependency Types

### JavaScript/Node.js
- `package.json` + `package-lock.json`
- Use: `npm outdated`, `npm audit`

### Python
- `requirements.txt`, `Pipfile`, `pyproject.toml`
- Use: `pip list --outdated`, `safety check`

### PHP
- `composer.json` + `composer.lock`
- Use: `composer outdated`

### Go
- `go.mod` + `go.sum`
- Use: `go list -u -m all`

### Ruby
- `Gemfile` + `Gemfile.lock`
- Use: `bundle outdated`

## Output

```markdown
# Dependency Update Report

## Updated (15 dependencies)
- axios: 0.27.2 → 1.6.2 ✅
- lodash: 4.17.20 → 4.17.21 ✅
- express: 4.17.1 → 4.18.2 ✅
...

## Failed (2 dependencies)
- webpack: 4.46.0 → 5.89.0 ❌
  - Breaking changes in config format
  - Manual intervention needed
- jest: 26.6.3 → 29.7.0 ❌
  - Test failures due to API changes
  - Requires code updates

## Skipped (3 dependencies)
- react: 17.0.2 (latest: 18.2.0)
  - Major version jump, requires planning
```
