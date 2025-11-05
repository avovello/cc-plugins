# Updated Repository Refactoring Plan v2

## Clarified Subagent Responsibilities

### Responsibility Separation Principle

**Architect Reviewer** = High-level system design
**Developer Reviewers** = Implementation quality and code-level patterns

---

## Subagent Organization

### 1. Architect Reviewer (System-Level)

**Responsibilities**:
- High-level software architecture patterns (MVC, microservices, layered, hexagonal, event-driven)
- System-wide dependencies and coupling analysis
- Maintainability and scalability at system level
- Clean Architecture principles (boundaries, dependency flow, use cases)
- Module/package/namespace organization
- API design at system level (REST, GraphQL, gRPC architecture)
- Database schema design and data modeling strategy
- Cross-cutting concerns strategy (logging, caching, authentication architecture)
- Service boundaries in distributed systems
- Integration patterns between components

**Does NOT Review**:
- Language-specific code quality
- SOLID/KISS/DRY at implementation level (that's developer reviewers' job)
- Framework-specific patterns
- Code formatting or style

---

### 2. Developer Reviewers (Implementation-Level)

Each developer reviewer focuses on **language/framework best practices and implementation quality**.

**Common Responsibilities for ALL Developer Reviewers**:
- **SOLID Principles**: Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion
- **KISS Principle**: Keep It Simple, Stupid - avoid over-engineering
- **DRY Principle**: Don't Repeat Yourself - eliminate code duplication
- Code readability and naming conventions
- Error handling and exception management (language-specific)
- Testing practices (unit tests, integration tests for that stack)
- Framework-specific best practices and conventions
- Proper use of language features and idioms
- Method/function design and cohesion
- Class/module design and coupling

**Does NOT Review**:
- System-wide architecture (that's architect's job)
- Security vulnerabilities (that's security reviewer's job)
- Performance optimization (that's performance reviewer's job)
- Infrastructure concerns (that's devops reviewer's job)

---

## Review Plugin Subagent Structure

### Core Review Team (6 Subagents)

```
plugins/review/agents/
├── architect-reviewer.md           # System architecture & design
├── security-reviewer.md            # Security vulnerabilities & OWASP
├── performance-reviewer.md         # Performance & optimization
├── backend-reviewer.md             # Backend code (multi-language)
├── frontend-reviewer.md            # Frontend code (multi-framework)
└── devops-reviewer.md             # Infrastructure & deployment
```

### Detailed Subagent Breakdown

#### 1. **architect-reviewer.md**
- System architecture patterns
- Clean Architecture boundaries
- Dependency flow and coupling
- Service design and boundaries
- Data modeling strategy
- Cross-cutting concerns architecture

#### 2. **security-reviewer.md**
- OWASP Top 10 vulnerabilities
- Input validation and sanitization
- Authentication and authorization implementation
- Secrets management
- SQL injection, XSS, CSRF prevention
- Security headers and configurations
- Cryptography usage

#### 3. **performance-reviewer.md**
- Performance bottlenecks
- Algorithm complexity (O(n²) → O(n log n))
- Database query optimization
- N+1 query problems
- Caching opportunities
- Memory leaks
- Resource management (connections, file handles)

#### 4. **backend-reviewer.md** (Multi-language)

Covers: PHP, Python, Node.js, Go, Java, Ruby, Rust, C#, Bash scripts

**Language-Specific Sections**:

**PHP:**
- PSR standards (PSR-1, PSR-2, PSR-4, PSR-12)
- Laravel/Symfony best practices
- Composer dependency management
- PHP 8+ features usage (typed properties, match, null-safe operator)
- SOLID, KISS, DRY principles in PHP
- Error handling (try-catch, custom exceptions)
- Type hints and return types

**Python:**
- PEP 8 style guide
- PEP 484 type hints
- Django/Flask/FastAPI best practices
- Pythonic idioms (list comprehensions, context managers)
- SOLID, KISS, DRY principles in Python
- Virtual environment and dependencies
- Error handling (exceptions, context managers)

**Node.js:**
- JavaScript/TypeScript best practices
- Express/NestJS/Fastify patterns
- Async/await vs promises vs callbacks
- NPM/Yarn dependency management
- SOLID, KISS, DRY principles in Node
- Error handling (try-catch, error-first callbacks)
- TypeScript strict mode usage

**Bash/Shell Scripts:**
- Shellcheck compliance
- POSIX compatibility
- Error handling (set -e, set -u, set -o pipefail)
- Quoting and variable expansion
- Script organization and functions

**Go:**
- Go idioms and conventions
- Error handling (error returns, not exceptions)
- Goroutines and channel patterns
- Interface design
- SOLID, KISS, DRY principles in Go

**Java:**
- Java coding standards
- Spring Boot best practices
- Dependency injection patterns
- Stream API usage
- SOLID, KISS, DRY principles in Java

#### 5. **frontend-reviewer.md** (Multi-framework)

Covers: React, Vue, Angular, Svelte, vanilla JavaScript, HTML, CSS

**Framework-Specific Sections**:

**React:**
- React hooks best practices
- Component composition patterns
- State management (Context, Redux, Zustand)
- Performance optimization (memo, useMemo, useCallback)
- SOLID, KISS, DRY principles in React
- Custom hooks patterns

**Vue:**
- Vue 3 Composition API
- Component design patterns
- Reactivity system proper usage
- Pinia/Vuex patterns
- SOLID, KISS, DRY principles in Vue

**General Frontend:**
- HTML semantic markup
- CSS best practices (BEM, CSS Modules, Tailwind patterns)
- Accessibility (WCAG, ARIA)
- JavaScript ES6+ features
- DOM manipulation best practices
- Event handling patterns
- SOLID, KISS, DRY principles in JavaScript

#### 6. **devops-reviewer.md**

Covers: Docker, Kubernetes, CI/CD, Infrastructure as Code

**Responsibilities**:
- Dockerfile best practices (multi-stage builds, layer optimization)
- Docker Compose configuration
- Kubernetes manifests (deployments, services, ingress)
- Helm charts structure
- CI/CD pipeline configuration (GitHub Actions, GitLab CI, Jenkins)
- Infrastructure as Code (Terraform, CloudFormation, Ansible)
- Configuration management
- Secrets management in deployments
- Health checks and readiness probes
- Resource limits and requests

---

## How Responsibilities Don't Overlap

### Example: Database Code Review

**Scenario**: Reviewing a database query in a Laravel controller

**Architect Reviewer** evaluates:
- Is the repository pattern correctly used?
- Are the database boundaries properly separated from business logic?
- Is this the right data modeling approach for the system?

**Backend Reviewer (PHP)** evaluates:
- Is this Laravel query following Eloquent best practices?
- Is the query builder properly used?
- Are relationships properly defined?
- Is there code duplication?
- SOLID: Is this following Single Responsibility?

**Performance Reviewer** evaluates:
- Is this query efficient?
- N+1 query problem?
- Missing indexes?
- Should this be cached?

**Security Reviewer** evaluates:
- Is user input properly sanitized?
- SQL injection vulnerabilities?
- Are sensitive fields properly protected?

---

### Example: React Component Review

**Scenario**: Reviewing a React component with state management

**Architect Reviewer** evaluates:
- Is the component properly placed in the system architecture?
- Does it respect the UI layer boundaries?
- Is the data flow architecture correct?

**Frontend Reviewer (React)** evaluates:
- Are hooks used correctly?
- Is the component too complex? (SRP violation)
- Is there code duplication? (DRY violation)
- Are props properly typed?
- KISS: Is this over-engineered?

**Performance Reviewer** evaluates:
- Unnecessary re-renders?
- Should useMemo/useCallback be used?
- Virtual scrolling needed?

**Security Reviewer** evaluates:
- XSS vulnerabilities?
- Is user input sanitized?
- Are secrets exposed in frontend code?

---

## Updated Plugin Structure

```
plugins/
│
├── investigation/
│   ├── .claude-plugin/
│   │   └── plugin.json
│   ├── commands/
│   │   └── investigate.md
│   ├── agents/
│   │   ├── explorer.md              # Maps codebase structure
│   │   ├── analyzer.md              # Analyzes patterns and tech debt
│   │   └── documenter.md            # Creates documentation
│   └── README.md
│
├── research/
│   ├── .claude-plugin/
│   │   └── plugin.json
│   ├── commands/
│   │   └── research.md
│   ├── agents/
│   │   ├── researcher.md            # Gathers information
│   │   ├── evaluator.md             # Evaluates options
│   │   └── comparator.md            # Comparative analysis
│   └── README.md
│
├── review/
│   ├── .claude-plugin/
│   │   └── plugin.json
│   ├── commands/
│   │   └── review.md
│   ├── agents/
│   │   ├── architect-reviewer.md    # ⭐ System architecture
│   │   ├── security-reviewer.md     # ⭐ Security vulnerabilities
│   │   ├── performance-reviewer.md  # ⭐ Performance optimization
│   │   ├── backend-reviewer.md      # ⭐ Backend implementation (PHP, Python, Node, Go, Java, Bash)
│   │   ├── frontend-reviewer.md     # ⭐ Frontend implementation (React, Vue, Angular, JS/HTML/CSS)
│   │   └── devops-reviewer.md       # ⭐ Infrastructure & deployment
│   └── README.md
│
├── feature/
│   ├── .claude-plugin/
│   │   └── plugin.json
│   ├── commands/
│   │   └── feature.md
│   ├── agents/
│   │   ├── planner.md               # Creates feature plans
│   │   ├── implementer.md           # Implements features
│   │   ├── tester.md                # Tests implementation
│   │   ├── bug-analyzer.md          # Analyzes test failures
│   │   ├── reviewer.md              # Reviews implementation
│   │   └── documenter.md            # Documents features
│   └── README.md
│
└── bugfix/
    ├── .claude-plugin/
    │   └── plugin.json
    ├── commands/
    │   └── bugfix.md
    ├── agents/
    │   ├── reproducer.md            # Reproduces bugs
    │   ├── root-cause-detective.md  # Identifies root causes
    │   ├── fixer.md                 # Implements fixes
    │   ├── tester.md                # Tests fixes
    │   └── regression-tester.md     # Regression testing
    └── README.md
```

---

## Review Command Workflow

The `/review` command will:

```markdown
1. **Analyze Changed Files**
   - Get git diff
   - Categorize files by type (backend, frontend, config, etc.)
   - Identify which reviewers are needed

2. **Launch Parallel Reviewers** (Dynamic based on file types)

   Always launch:
   - Architect Reviewer (always review architecture implications)
   - Security Reviewer (always check for vulnerabilities)

   Conditionally launch based on file types:
   - Backend Reviewer (if backend files changed)
   - Frontend Reviewer (if frontend files changed)
   - DevOps Reviewer (if Docker/K8s/CI files changed)
   - Performance Reviewer (if performance-critical code changed)

   Each reviewer:
   - Reviews only their domain
   - Assigns confidence score (0-100) to each issue
   - Provides file:line citations
   - Categorizes severity (critical/high/medium/low)

3. **Consolidate Results**
   - Merge all reviewer outputs
   - Filter issues with confidence < 80
   - Remove duplicates
   - Group by severity

4. **Apply Quality Gates**
   - Critical issues: 0 allowed
   - High issues: ≤ 2 allowed
   - If gates fail, proceed to fix loop

5. **Fix Loop** (max 2 iterations)
   - Apply fixes for critical/high issues
   - Re-run affected reviewers
   - If gates still fail and iterations < 2: repeat
   - If iterations ≥ 2: report and ask for guidance

6. **Generate Report**
   - Issues by severity
   - Reviewer citations
   - Statistics (files reviewed, issues found, etc.)
```

---

## Backend Reviewer Structure

```markdown
# Backend Reviewer

## Purpose
Review backend implementation code for language/framework best practices, SOLID/KISS/DRY principles, and code quality.

## Supported Languages & Frameworks
- **PHP**: Laravel, Symfony, PSR standards
- **Python**: Django, Flask, FastAPI, PEP standards
- **Node.js**: Express, NestJS, TypeScript
- **Go**: Standard library, idiomatic Go
- **Java**: Spring Boot, Jakarta EE
- **Bash**: Shell scripts, automation

## Responsibilities

### SOLID Principles
- **Single Responsibility**: Each class/function has one reason to change
- **Open/Closed**: Open for extension, closed for modification
- **Liskov Substitution**: Subtypes must be substitutable for base types
- **Interface Segregation**: Clients shouldn't depend on interfaces they don't use
- **Dependency Inversion**: Depend on abstractions, not concretions

### KISS Principle
- Is the solution unnecessarily complex?
- Can it be simplified?
- Is over-engineering present?

### DRY Principle
- Code duplication?
- Should logic be extracted to shared functions?
- Are constants/configs repeated?

### Language-Specific Review

#### PHP Review Checklist
- [ ] PSR-1, PSR-2, PSR-4, PSR-12 compliance
- [ ] Type hints on parameters and return types
- [ ] Proper exception handling
- [ ] Laravel/Symfony conventions followed
- [ ] Dependency injection used properly
- [ ] Eloquent relationships properly defined
- [ ] No SQL injection vulnerabilities (use query builder)

#### Python Review Checklist
- [ ] PEP 8 style compliance
- [ ] Type hints (PEP 484) used
- [ ] Docstrings for functions/classes
- [ ] Context managers for resources
- [ ] List comprehensions vs loops (when appropriate)
- [ ] Django/Flask patterns followed
- [ ] Proper exception handling

#### Node.js Review Checklist
- [ ] Async/await used consistently
- [ ] Error handling in async code
- [ ] TypeScript types properly used
- [ ] Promise chains avoided (use async/await)
- [ ] Express middleware patterns
- [ ] Environment variables properly used

#### Bash Review Checklist
- [ ] Shellcheck warnings addressed
- [ ] set -euo pipefail used
- [ ] Variables properly quoted
- [ ] Functions used for organization
- [ ] Error handling present

## What NOT to Review
- System architecture (Architect Reviewer's job)
- Security vulnerabilities (Security Reviewer's job)
- Performance optimization (Performance Reviewer's job)
- Infrastructure configs (DevOps Reviewer's job)

## Confidence Scoring
- **90-100**: Clear violation of SOLID/KISS/DRY or language best practice
- **80-89**: Likely code quality issue that should be addressed
- **70-79**: Questionable pattern, could be improved
- **Below 70**: Style preference (don't report)

## Output Format
For each issue:
- **Description**: Brief (1-2 sentences)
- **Principle Violated**: SOLID/KISS/DRY/Language Best Practice
- **Language**: Which language this applies to
- **File and Line**: file.php:123 with GitHub permalink
- **Confidence**: 80-100
- **Severity**: critical/high/medium/low
- **Suggested Fix**: Brief suggestion
```

---

## Implementation Plan

### Phase 1: Clean Up
- Remove setup.py, pyproject.toml, src/ directory
- Remove old plugin structure files
- Keep only: README.md, LICENSE, CONTRIBUTING.md, QUICKSTART.md

### Phase 2: Create Plugin Structure (Per Plugin)

For each plugin (investigation, research, review, feature, bugfix):

1. **Create directory structure**
   ```
   mkdir -p plugins/{plugin-name}/.claude-plugin
   mkdir -p plugins/{plugin-name}/commands
   mkdir -p plugins/{plugin-name}/agents
   ```

2. **Create plugin.json**
3. **Create command markdown**
4. **Create agent markdowns**
5. **Create plugin README**

### Phase 3: Implement Review Plugin (Most Complex)

Create 6 reviewer agents with clear responsibilities:
1. architect-reviewer.md (system design)
2. security-reviewer.md (vulnerabilities)
3. performance-reviewer.md (optimization)
4. backend-reviewer.md (PHP, Python, Node, Go, Java, Bash)
5. frontend-reviewer.md (React, Vue, Angular, JS, HTML, CSS)
6. devops-reviewer.md (Docker, K8s, CI/CD)

### Phase 4: Documentation
- Update main README
- Update QUICKSTART
- Create plugin development guide
- Add examples

### Phase 5: Commit & Push

---

## Questions Before Proceeding

1. **Is this responsibility separation clear?**
   - Architect = system-level
   - Developers = implementation-level with SOLID/KISS/DRY
   - Specialists = security, performance, devops

2. **Should backend-reviewer.md be split into separate files** (one per language)?
   - Option A: Single backend-reviewer.md with sections per language
   - Option B: backend-php-reviewer.md, backend-python-reviewer.md, etc.

   I recommend **Option A** (single file) because:
   - The reviewer can auto-detect language from file extension
   - Principles (SOLID/KISS/DRY) are the same across languages
   - Easier to maintain

3. **Any other subagents needed?**
   - Database reviewer? (for SQL query quality, migrations)
   - API reviewer? (for REST API design - though this might overlap with architect)

4. **Ready to start implementation?**

---

## Next Steps

Once approved:
1. Execute Phase 1 (clean up)
2. Create Review plugin first (most complex, validates the structure)
3. Create remaining plugins
4. Update documentation
5. Commit and push

**Estimated time**: 3-4 hours

Ready to proceed?
