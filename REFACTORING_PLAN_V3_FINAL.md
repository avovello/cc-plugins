# Final Refactoring Plan v3 - Highly Granular Subagents

## Core Principle: Single Responsibility Per Subagent

**Every subagent has ONE clear job with precise boundaries.**

Each subagent definition includes:
- ✅ **DOES**: Exact responsibilities
- ❌ **DOES NOT**: What's explicitly out of scope
- 🎯 **Invoked When**: When to use this agent
- 📤 **Returns**: What output it produces

---

# Repository Structure

```
claude-code-marketplace/
├── README.md
├── QUICKSTART.md
├── CONTRIBUTING.md
├── LICENSE
│
└── plugins/
    ├── investigation/
    ├── research/
    ├── review/
    ├── feature/
    └── bugfix/
```

---

# Plugin 1: Investigation Plugin 🔍

**Purpose**: Deep codebase exploration and understanding

## Directory Structure

```
plugins/investigation/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── investigate.md
├── agents/
│   ├── structure-mapper.md
│   ├── dependency-analyzer.md
│   ├── pattern-detector.md
│   ├── tech-debt-assessor.md
│   └── documentation-generator.md
└── README.md
```

## Subagents

### 1. structure-mapper.md
- ✅ **DOES**: Maps directory structure, file organization, naming conventions
- ❌ **DOES NOT**: Analyze code content, dependencies, or patterns
- 🎯 **Invoked When**: Need to understand project layout
- 📤 **Returns**: Directory tree, file counts by type, organization patterns

### 2. dependency-analyzer.md
- ✅ **DOES**: Analyzes external dependencies, versions, dependency tree, unused dependencies
- ❌ **DOES NOT**: Analyze code structure, patterns, or internal module relationships
- 🎯 **Invoked When**: Need to understand external dependencies
- 📤 **Returns**: Dependency list, version info, dependency graph, issues

### 3. pattern-detector.md
- ✅ **DOES**: Identifies code patterns, design patterns in use, coding conventions
- ❌ **DOES NOT**: Assess quality, find bugs, or analyze architecture
- 🎯 **Invoked When**: Need to understand what patterns are used
- 📤 **Returns**: List of patterns found, where they're used, consistency analysis

### 4. tech-debt-assessor.md
- ✅ **DOES**: Identifies technical debt, TODO/FIXME comments, deprecated code, code smells
- ❌ **DOES NOT**: Fix issues, analyze architecture, or find bugs
- 🎯 **Invoked When**: Need to assess technical debt
- 📤 **Returns**: Tech debt inventory, severity ratings, locations

### 5. documentation-generator.md
- ✅ **DOES**: Creates architecture docs, component docs, API docs from code analysis
- ❌ **DOES NOT**: Analyze code, find issues, or make recommendations
- 🎯 **Invoked When**: Need to generate documentation
- 📤 **Returns**: Markdown documentation files (ARCHITECTURE.md, COMPONENTS.md, API.md)

---

# Plugin 2: Research Plugin 📚

**Purpose**: Technology research and comparative analysis

## Directory Structure

```
plugins/research/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── research.md
├── agents/
│   ├── information-gatherer.md
│   ├── feature-comparator.md
│   ├── performance-evaluator.md
│   ├── ecosystem-evaluator.md
│   └── recommendation-synthesizer.md
└── README.md
```

## Subagents

### 1. information-gatherer.md
- ✅ **DOES**: Gathers raw information about technologies, features, capabilities
- ❌ **DOES NOT**: Compare, evaluate, or make recommendations
- 🎯 **Invoked When**: Need to collect information about technologies
- 📤 **Returns**: Raw data about each technology (features, versions, docs links)

### 2. feature-comparator.md
- ✅ **DOES**: Compares features side-by-side, creates comparison matrices
- ❌ **DOES NOT**: Evaluate quality, performance, or make recommendations
- 🎯 **Invoked When**: Need to compare what features each option has
- 📤 **Returns**: Feature comparison matrix

### 3. performance-evaluator.md
- ✅ **DOES**: Evaluates performance characteristics, benchmarks, scalability
- ❌ **DOES NOT**: Compare features, evaluate ecosystem, or make final recommendations
- 🎯 **Invoked When**: Need to understand performance implications
- 📤 **Returns**: Performance analysis, benchmark data, scalability assessment

### 4. ecosystem-evaluator.md
- ✅ **DOES**: Evaluates community size, maintenance status, plugin ecosystem, learning resources
- ❌ **DOES NOT**: Evaluate features, performance, or make final recommendations
- 🎯 **Invoked When**: Need to assess ecosystem health
- 📤 **Returns**: Ecosystem health report, community metrics, resource availability

### 5. recommendation-synthesizer.md
- ✅ **DOES**: Synthesizes all research into final recommendation with reasoning
- ❌ **DOES NOT**: Gather information, compare features, or evaluate
- 🎯 **Invoked When**: All research complete, need final recommendation
- 📤 **Returns**: Recommendation with reasoning, trade-offs, use-case fit

---

# Plugin 3: Review Plugin 👁️

**Purpose**: Multi-perspective code review

## Directory Structure

```
plugins/review/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── review.md
├── agents/
│   ├── architect-reviewer.md
│   ├── security-authentication-reviewer.md
│   ├── security-input-reviewer.md
│   ├── security-crypto-reviewer.md
│   ├── performance-algorithm-reviewer.md
│   ├── performance-database-reviewer.md
│   ├── performance-resource-reviewer.md
│   ├── backend-php-reviewer.md
│   ├── backend-python-reviewer.md
│   ├── backend-nodejs-reviewer.md
│   ├── backend-go-reviewer.md
│   ├── backend-bash-reviewer.md
│   ├── frontend-react-reviewer.md
│   ├── frontend-vue-reviewer.md
│   ├── frontend-html-reviewer.md
│   ├── frontend-css-reviewer.md
│   ├── devops-docker-reviewer.md
│   ├── devops-kubernetes-reviewer.md
│   └── devops-cicd-reviewer.md
└── README.md
```

## Subagents

### Architecture

#### architect-reviewer.md
- ✅ **DOES**: Reviews system architecture patterns, service boundaries, dependency flow, Clean Architecture boundaries, module organization
- ❌ **DOES NOT**: Review code implementation, security, performance, or infrastructure
- 🎯 **Invoked When**: Always (architecture implications in any change)
- 📤 **Returns**: Architecture issues with confidence scores (80-100)

**Examples**:
- ✅ Reviews: "Service boundary violation - business logic in controller"
- ❌ Does NOT review: "This loop is O(n²)" (that's performance reviewer)
- ❌ Does NOT review: "Missing input validation" (that's security reviewer)
- ❌ Does NOT review: "Variable name unclear" (that's language reviewer)

---

### Security (Split by Domain)

#### security-authentication-reviewer.md
- ✅ **DOES**: Reviews authentication, authorization, session management, JWT handling, OAuth flows
- ❌ **DOES NOT**: Review input validation, cryptography, or general vulnerabilities
- 🎯 **Invoked When**: Auth-related code changes
- 📤 **Returns**: Auth security issues with confidence scores

**Examples**:
- ✅ Reviews: "JWT token not properly validated"
- ✅ Reviews: "Missing authorization check before data access"
- ❌ Does NOT review: "SQL injection vulnerability" (that's input reviewer)
- ❌ Does NOT review: "Weak encryption algorithm" (that's crypto reviewer)

#### security-input-reviewer.md
- ✅ **DOES**: Reviews input validation, sanitization, SQL injection, XSS, command injection, path traversal
- ❌ **DOES NOT**: Review authentication, cryptography, or authorization
- 🎯 **Invoked When**: Code that handles user input
- 📤 **Returns**: Input security issues with confidence scores

**Examples**:
- ✅ Reviews: "User input not sanitized before SQL query"
- ✅ Reviews: "XSS vulnerability in HTML rendering"
- ❌ Does NOT review: "Weak password policy" (that's auth reviewer)
- ❌ Does NOT review: "Using MD5 for hashing" (that's crypto reviewer)

#### security-crypto-reviewer.md
- ✅ **DOES**: Reviews cryptography usage, hashing algorithms, encryption, secrets management, certificate handling
- ❌ **DOES NOT**: Review authentication flows, input validation, or authorization
- 🎯 **Invoked When**: Cryptography-related code changes
- 📤 **Returns**: Cryptography issues with confidence scores

**Examples**:
- ✅ Reviews: "Using deprecated MD5 hashing"
- ✅ Reviews: "Hardcoded encryption key"
- ❌ Does NOT review: "Missing login attempt limiting" (that's auth reviewer)
- ❌ Does NOT review: "SQL injection" (that's input reviewer)

---

### Performance (Split by Domain)

#### performance-algorithm-reviewer.md
- ✅ **DOES**: Reviews algorithm complexity, nested loops, recursion depth, inefficient algorithms
- ❌ **DOES NOT**: Review database queries, resource management, or caching
- 🎯 **Invoked When**: Algorithm-heavy code changes
- 📤 **Returns**: Algorithm performance issues with confidence scores

**Examples**:
- ✅ Reviews: "O(n²) loop could be O(n)"
- ✅ Reviews: "Unnecessary recursion"
- ❌ Does NOT review: "N+1 query problem" (that's database reviewer)
- ❌ Does NOT review: "Memory leak" (that's resource reviewer)

#### performance-database-reviewer.md
- ✅ **DOES**: Reviews database queries, indexes, N+1 problems, query optimization, ORM usage
- ❌ **DOES NOT**: Review algorithm complexity, memory management, or caching
- 🎯 **Invoked When**: Database interaction code changes
- 📤 **Returns**: Database performance issues with confidence scores

**Examples**:
- ✅ Reviews: "N+1 query in loop"
- ✅ Reviews: "Missing index on frequently queried column"
- ❌ Does NOT review: "Inefficient sorting algorithm" (that's algorithm reviewer)
- ❌ Does NOT review: "Missing cache" (could add caching reviewer if needed)

#### performance-resource-reviewer.md
- ✅ **DOES**: Reviews memory leaks, connection pooling, file handle management, resource cleanup
- ❌ **DOES NOT**: Review algorithms or database queries
- 🎯 **Invoked When**: Resource management code changes
- 📤 **Returns**: Resource management issues with confidence scores

**Examples**:
- ✅ Reviews: "File handle not closed"
- ✅ Reviews: "Database connection not returned to pool"
- ❌ Does NOT review: "Slow sorting algorithm" (that's algorithm reviewer)
- ❌ Does NOT review: "Missing query index" (that's database reviewer)

---

### Backend (Split by Language)

#### backend-php-reviewer.md
- ✅ **DOES**: Reviews PHP code for PSR standards, SOLID/KISS/DRY, Laravel/Symfony patterns, PHP 8+ features, type hints
- ❌ **DOES NOT**: Review architecture, security, performance, or other languages
- 🎯 **Invoked When**: PHP files changed
- 📤 **Returns**: PHP code quality issues with confidence scores

**Examples**:
- ✅ Reviews: "Missing return type hint"
- ✅ Reviews: "Class violates Single Responsibility Principle"
- ✅ Reviews: "Not following PSR-12 standard"
- ❌ Does NOT review: "Architecture boundary violation" (that's architect)
- ❌ Does NOT review: "SQL injection" (that's security-input)
- ❌ Does NOT review: "N+1 query" (that's performance-database)

#### backend-python-reviewer.md
- ✅ **DOES**: Reviews Python code for PEP 8, SOLID/KISS/DRY, Django/Flask patterns, type hints, Pythonic idioms
- ❌ **DOES NOT**: Review architecture, security, performance, or other languages
- 🎯 **Invoked When**: Python files changed
- 📤 **Returns**: Python code quality issues with confidence scores

**Examples**:
- ✅ Reviews: "Missing type hints"
- ✅ Reviews: "Not using context manager for file"
- ✅ Reviews: "Function too long, violates SRP"
- ❌ Does NOT review: "Service boundary violation" (that's architect)
- ❌ Does NOT review: "Missing input validation" (that's security-input)

#### backend-nodejs-reviewer.md
- ✅ **DOES**: Reviews Node.js/JavaScript/TypeScript for async/await patterns, SOLID/KISS/DRY, Express/NestJS patterns, TypeScript types
- ❌ **DOES NOT**: Review architecture, security, performance, or other languages
- 🎯 **Invoked When**: Node.js/JavaScript/TypeScript files changed
- 📤 **Returns**: Node.js code quality issues with confidence scores

**Examples**:
- ✅ Reviews: "Using callbacks instead of async/await"
- ✅ Reviews: "Missing TypeScript types"
- ✅ Reviews: "Promise chain could be simplified"
- ❌ Does NOT review: "Architecture pattern violation" (that's architect)
- ❌ Does NOT review: "XSS vulnerability" (that's security-input)

#### backend-go-reviewer.md
- ✅ **DOES**: Reviews Go code for Go idioms, SOLID/KISS/DRY, error handling, goroutines, interfaces
- ❌ **DOES NOT**: Review architecture, security, performance, or other languages
- 🎯 **Invoked When**: Go files changed
- 📤 **Returns**: Go code quality issues with confidence scores

**Examples**:
- ✅ Reviews: "Error not checked"
- ✅ Reviews: "Not following Go naming conventions"
- ✅ Reviews: "Goroutine leak potential"
- ❌ Does NOT review: "System design issue" (that's architect)

#### backend-bash-reviewer.md
- ✅ **DOES**: Reviews Bash scripts for Shellcheck compliance, POSIX, error handling, quoting, script organization
- ❌ **DOES NOT**: Review architecture, security, performance, or other languages
- 🎯 **Invoked When**: Bash/shell scripts changed
- 📤 **Returns**: Bash script quality issues with confidence scores

**Examples**:
- ✅ Reviews: "Missing set -euo pipefail"
- ✅ Reviews: "Variable not quoted"
- ✅ Reviews: "Function too complex"
- ❌ Does NOT review: "Command injection vulnerability" (that's security-input)

---

### Frontend (Split by Technology)

#### frontend-react-reviewer.md
- ✅ **DOES**: Reviews React code for hooks best practices, component composition, SOLID/KISS/DRY, state management patterns
- ❌ **DOES NOT**: Review Vue, Angular, HTML, CSS, or backend
- 🎯 **Invoked When**: React (.jsx, .tsx) files changed
- 📤 **Returns**: React code quality issues with confidence scores

**Examples**:
- ✅ Reviews: "Missing dependency in useEffect"
- ✅ Reviews: "Component too large, violates SRP"
- ✅ Reviews: "Should use useMemo for expensive calculation"
- ❌ Does NOT review: "XSS vulnerability" (that's security-input)
- ❌ Does NOT review: "CSS specificity issue" (that's frontend-css)

#### frontend-vue-reviewer.md
- ✅ **DOES**: Reviews Vue code for Composition API, component patterns, SOLID/KISS/DRY, reactivity best practices
- ❌ **DOES NOT**: Review React, Angular, HTML, CSS, or backend
- 🎯 **Invoked When**: Vue (.vue) files changed
- 📤 **Returns**: Vue code quality issues with confidence scores

**Examples**:
- ✅ Reviews: "Should use computed instead of method"
- ✅ Reviews: "Component too complex"
- ✅ Reviews: "Not properly using reactive refs"
- ❌ Does NOT review: "Security issue" (that's security reviewer)

#### frontend-html-reviewer.md
- ✅ **DOES**: Reviews HTML for semantic markup, accessibility (ARIA), proper tag usage, SEO
- ❌ **DOES NOT**: Review React/Vue components, CSS, or JavaScript
- 🎯 **Invoked When**: HTML files or template sections changed
- 📤 **Returns**: HTML quality issues with confidence scores

**Examples**:
- ✅ Reviews: "Missing alt attribute on image"
- ✅ Reviews: "Should use semantic <nav> instead of <div>"
- ✅ Reviews: "Missing ARIA labels for screen readers"
- ❌ Does NOT review: "React component structure" (that's frontend-react)

#### frontend-css-reviewer.md
- ✅ **DOES**: Reviews CSS for best practices, BEM naming, specificity issues, responsive design, Tailwind patterns
- ❌ **DOES NOT**: Review HTML structure, JavaScript, or components
- 🎯 **Invoked When**: CSS/SCSS/styled-components files changed
- 📤 **Returns**: CSS quality issues with confidence scores

**Examples**:
- ✅ Reviews: "!important overuse"
- ✅ Reviews: "Not following BEM naming"
- ✅ Reviews: "Missing mobile breakpoints"
- ❌ Does NOT review: "Component logic" (that's framework reviewer)

---

### DevOps (Split by Technology)

#### devops-docker-reviewer.md
- ✅ **DOES**: Reviews Dockerfiles for best practices, multi-stage builds, layer optimization, security
- ❌ **DOES NOT**: Review Kubernetes, CI/CD, or application code
- 🎯 **Invoked When**: Dockerfile or docker-compose.yml changed
- 📤 **Returns**: Docker configuration issues with confidence scores

**Examples**:
- ✅ Reviews: "Not using multi-stage build"
- ✅ Reviews: "Running as root user"
- ✅ Reviews: "Large image size due to unnecessary files"
- ❌ Does NOT review: "Kubernetes deployment config" (that's devops-kubernetes)

#### devops-kubernetes-reviewer.md
- ✅ **DOES**: Reviews K8s manifests, Helm charts, resource limits, health checks, service definitions
- ❌ **DOES NOT**: Review Docker, CI/CD, or application code
- 🎯 **Invoked When**: K8s YAML or Helm charts changed
- 📤 **Returns**: Kubernetes configuration issues with confidence scores

**Examples**:
- ✅ Reviews: "Missing resource limits"
- ✅ Reviews: "No liveness probe defined"
- ✅ Reviews: "Service selector doesn't match pods"
- ❌ Does NOT review: "Dockerfile issues" (that's devops-docker)

#### devops-cicd-reviewer.md
- ✅ **DOES**: Reviews CI/CD pipelines, GitHub Actions, GitLab CI, Jenkins configs, deployment strategies
- ❌ **DOES NOT**: Review Docker, Kubernetes, or application code
- 🎯 **Invoked When**: CI/CD config files changed
- 📤 **Returns**: CI/CD configuration issues with confidence scores

**Examples**:
- ✅ Reviews: "Missing test step in pipeline"
- ✅ Reviews: "No caching for dependencies"
- ✅ Reviews: "Secrets hardcoded in workflow"
- ❌ Does NOT review: "K8s deployment manifest" (that's devops-kubernetes)

---

# Plugin 4: Feature Plugin ✨

**Purpose**: Complete feature development lifecycle

## Directory Structure

```
plugins/feature/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── feature.md
├── agents/
│   ├── architecture-planner.md
│   ├── implementation-planner.md
│   ├── code-implementer.md
│   ├── test-writer.md
│   ├── test-runner.md
│   ├── test-failure-analyzer.md
│   ├── code-quality-reviewer.md
│   └── implementation-documenter.md
└── README.md
```

## Subagents

### 1. architecture-planner.md
- ✅ **DOES**: Plans architecture changes, service boundaries, component design, data flow
- ❌ **DOES NOT**: Plan implementation steps, write code, or write tests
- 🎯 **Invoked When**: Starting feature planning
- 📤 **Returns**: Architecture plan with components, boundaries, data flow

### 2. implementation-planner.md
- ✅ **DOES**: Plans implementation steps, file changes, execution order, dependencies between steps
- ❌ **DOES NOT**: Plan architecture, write code, or write tests
- 🎯 **Invoked When**: After architecture planning
- 📤 **Returns**: Step-by-step implementation plan with file list and order

### 3. code-implementer.md
- ✅ **DOES**: Writes production code following the implementation plan
- ❌ **DOES NOT**: Plan, write tests, review code, or document
- 🎯 **Invoked When**: Implementation plan approved
- 📤 **Returns**: Production code changes

### 4. test-writer.md
- ✅ **DOES**: Writes unit tests, integration tests, test fixtures
- ❌ **DOES NOT**: Write production code, run tests, or analyze failures
- 🎯 **Invoked When**: Production code implemented
- 📤 **Returns**: Test files and fixtures

### 5. test-runner.md
- ✅ **DOES**: Executes tests, captures output, parses results
- ❌ **DOES NOT**: Write tests, analyze failures, or fix bugs
- 🎯 **Invoked When**: Tests need to be executed
- 📤 **Returns**: Test results with pass/fail status

### 6. test-failure-analyzer.md
- ✅ **DOES**: Analyzes why tests failed, identifies root causes, suggests fixes
- ❌ **DOES NOT**: Run tests, write tests, or implement fixes
- 🎯 **Invoked When**: Tests fail
- 📤 **Returns**: Analysis of failures with suggested fixes

### 7. code-quality-reviewer.md
- ✅ **DOES**: Reviews code for SOLID/KISS/DRY, best practices, code smells
- ❌ **DOES NOT**: Write code, run tests, or implement fixes
- 🎯 **Invoked When**: Code ready for review
- 📤 **Returns**: Code review feedback with severity ratings

### 8. implementation-documenter.md
- ✅ **DOES**: Documents the implementation, updates README, creates API docs, updates CHANGELOG
- ❌ **DOES NOT**: Write code, review code, or plan features
- 🎯 **Invoked When**: Implementation complete and reviewed
- 📤 **Returns**: Documentation files (README updates, API docs, CHANGELOG)

---

# Plugin 5: Bugfix Plugin 🐛

**Purpose**: Systematic bug fixing with root cause analysis

## Directory Structure

```
plugins/bugfix/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   └── bugfix.md
├── agents/
│   ├── bug-reproducer.md
│   ├── root-cause-analyst.md
│   ├── impact-assessor.md
│   ├── fix-planner.md
│   ├── fix-implementer.md
│   ├── fix-tester.md
│   └── regression-tester.md
└── README.md
```

## Subagents

### 1. bug-reproducer.md
- ✅ **DOES**: Reproduces the bug, creates minimal reproduction case, writes failing test
- ❌ **DOES NOT**: Analyze root cause, plan fix, or implement fix
- 🎯 **Invoked When**: Bug reported
- 📤 **Returns**: Reproduction steps and failing test

### 2. root-cause-analyst.md
- ✅ **DOES**: Analyzes the bug to find root cause, traces execution flow, identifies exact location
- ❌ **DOES NOT**: Reproduce bug, assess impact, or plan fix
- 🎯 **Invoked When**: Bug reproduced
- 📤 **Returns**: Root cause analysis with exact file:line location

### 3. impact-assessor.md
- ✅ **DOES**: Assesses bug impact, severity, affected users, related components
- ❌ **DOES NOT**: Find root cause, plan fix, or implement fix
- 🎯 **Invoked When**: Root cause identified
- 📤 **Returns**: Impact assessment with severity rating

### 4. fix-planner.md
- ✅ **DOES**: Plans the fix approach, considers alternatives, identifies files to change
- ❌ **DOES NOT**: Implement fix, test fix, or assess impact
- 🎯 **Invoked When**: Root cause and impact known
- 📤 **Returns**: Fix plan with approach and file list

### 5. fix-implementer.md
- ✅ **DOES**: Implements the fix according to the plan
- ❌ **DOES NOT**: Plan fix, test fix, or check for regressions
- 🎯 **Invoked When**: Fix plan approved
- 📤 **Returns**: Fix implementation (code changes)

### 6. fix-tester.md
- ✅ **DOES**: Tests that the fix resolves the bug, runs reproduction test
- ❌ **DOES NOT**: Implement fix, check for regressions, or write new tests
- 🎯 **Invoked When**: Fix implemented
- 📤 **Returns**: Test results showing bug is fixed

### 7. regression-tester.md
- ✅ **DOES**: Runs full test suite to check for regressions caused by fix
- ❌ **DOES NOT**: Test the fix itself, implement changes, or analyze bugs
- 🎯 **Invoked When**: Fix tested and working
- 📤 **Returns**: Regression test results

---

# Subagent Responsibility Matrix

## Review Plugin Matrix

| Subagent | Architecture | Security | Performance | Code Quality | Language | Infrastructure |
|----------|-------------|----------|-------------|--------------|----------|----------------|
| architect-reviewer | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ |
| security-authentication-reviewer | ❌ | ✅ (Auth only) | ❌ | ❌ | ❌ | ❌ |
| security-input-reviewer | ❌ | ✅ (Input only) | ❌ | ❌ | ❌ | ❌ |
| security-crypto-reviewer | ❌ | ✅ (Crypto only) | ❌ | ❌ | ❌ | ❌ |
| performance-algorithm-reviewer | ❌ | ❌ | ✅ (Algorithms) | ❌ | ❌ | ❌ |
| performance-database-reviewer | ❌ | ❌ | ✅ (Database) | ❌ | ❌ | ❌ |
| performance-resource-reviewer | ❌ | ❌ | ✅ (Resources) | ❌ | ❌ | ❌ |
| backend-php-reviewer | ❌ | ❌ | ❌ | ✅ | ✅ (PHP) | ❌ |
| backend-python-reviewer | ❌ | ❌ | ❌ | ✅ | ✅ (Python) | ❌ |
| backend-nodejs-reviewer | ❌ | ❌ | ❌ | ✅ | ✅ (Node.js) | ❌ |
| backend-go-reviewer | ❌ | ❌ | ❌ | ✅ | ✅ (Go) | ❌ |
| backend-bash-reviewer | ❌ | ❌ | ❌ | ✅ | ✅ (Bash) | ❌ |
| frontend-react-reviewer | ❌ | ❌ | ❌ | ✅ | ✅ (React) | ❌ |
| frontend-vue-reviewer | ❌ | ❌ | ❌ | ✅ | ✅ (Vue) | ❌ |
| frontend-html-reviewer | ❌ | ❌ | ❌ | ✅ | ✅ (HTML) | ❌ |
| frontend-css-reviewer | ❌ | ❌ | ❌ | ✅ | ✅ (CSS) | ❌ |
| devops-docker-reviewer | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ (Docker) |
| devops-kubernetes-reviewer | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ (K8s) |
| devops-cicd-reviewer | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ (CI/CD) |

---

# Command Workflow Examples

## Review Command Workflow

```markdown
# Code Review Command

## Step 1: Analyze Changed Files

Determine which reviewers are needed:
- If ANY file changed → invoke architect-reviewer (always)
- If ANY file with user input → invoke security-input-reviewer
- If ANY file with auth logic → invoke security-authentication-reviewer
- If ANY file with crypto → invoke security-crypto-reviewer
- If ANY *.php file → invoke backend-php-reviewer
- If ANY *.py file → invoke backend-python-reviewer
- If ANY *.js/*.ts file (backend) → invoke backend-nodejs-reviewer
- If ANY *.go file → invoke backend-go-reviewer
- If ANY *.sh file → invoke backend-bash-reviewer
- If ANY *.jsx/*.tsx file → invoke frontend-react-reviewer
- If ANY *.vue file → invoke frontend-vue-reviewer
- If ANY *.html file → invoke frontend-html-reviewer
- If ANY *.css/*.scss file → invoke frontend-css-reviewer
- If ANY Dockerfile → invoke devops-docker-reviewer
- If ANY k8s/*.yaml file → invoke devops-kubernetes-reviewer
- If ANY .github/workflows/*.yml → invoke devops-cicd-reviewer
- If ANY algorithm-heavy code → invoke performance-algorithm-reviewer
- If ANY database queries → invoke performance-database-reviewer
- If ANY resource management → invoke performance-resource-reviewer

## Step 2: Launch Reviewers in Parallel

Each reviewer:
- Reviews ONLY their domain
- Assigns confidence score (0-100) to each issue
- Filters and returns only issues with confidence ≥ 80
- Categorizes as critical/high/medium/low

## Step 3: Consolidate Results

Merge all reviewer outputs
Group by severity

## Step 4: Apply Quality Gates

- Critical: 0 allowed
- High: ≤ 2 allowed
- If gates fail → proceed to fix loop

## Step 5: Fix Loop (max 2 iterations)

If quality gates fail:
- Apply fixes for critical/high issues
- Re-run affected reviewers only
- Check gates again
- If still failing and iterations < 2: repeat
- If iterations ≥ 2: report and ask for guidance

## Step 6: Generate Report

Create review report with all findings
```

---

# Implementation Steps

## Phase 1: Clean Up (30 min)
1. Remove setup.py, pyproject.toml
2. Remove src/ directory
3. Remove old plugin files (except docs)
4. Keep: README.md, LICENSE, CONTRIBUTING.md, QUICKSTART.md

## Phase 2: Create Base Plugin Structures (30 min)
For each plugin, create:
- Directory structure
- .claude-plugin/plugin.json
- commands/ directory
- agents/ directory
- README.md stub

## Phase 3: Implement Investigation Plugin (1 hour)
- Create investigate.md command
- Create 5 agent files with clear boundaries
- Update README

## Phase 4: Implement Research Plugin (1 hour)
- Create research.md command
- Create 5 agent files with clear boundaries
- Update README

## Phase 5: Implement Review Plugin (3 hours) - Most Complex
- Create review.md command
- Create 19 reviewer agent files:
  - 1 architect
  - 3 security (auth, input, crypto)
  - 3 performance (algorithm, database, resource)
  - 5 backend (PHP, Python, Node, Go, Bash)
  - 4 frontend (React, Vue, HTML, CSS)
  - 3 devops (Docker, K8s, CI/CD)
- Each with precise boundaries
- Update README

## Phase 6: Implement Feature Plugin (1.5 hours)
- Create feature.md command
- Create 8 agent files with clear boundaries
- Update README

## Phase 7: Implement Bugfix Plugin (1 hour)
- Create bugfix.md command
- Create 7 agent files with clear boundaries
- Update README

## Phase 8: Documentation (1 hour)
- Update main README
- Update QUICKSTART
- Create plugin development guide
- Add examples

## Phase 9: Commit & Push (30 min)
- Commit all changes
- Push to GitHub

**Total Estimated Time: 9-10 hours**

---

# Questions Before Implementation

1. **Review Plugin**: 19 reviewers OK, or should I consolidate some?
   - Could combine security reviewers into one with sections?
   - Could combine performance reviewers into one?
   - Current approach: Maximum granularity

2. **Backend Reviewers**: Should I add more languages?
   - Rust, C#, Ruby, Kotlin?

3. **Should I create a visual diagram** showing all subagents and their boundaries?

4. **Ready to start Phase 1** (clean up)?

---

This plan ensures **every single subagent** has ONE clear job with no overlap. Each knows exactly what it does and doesn't do.
