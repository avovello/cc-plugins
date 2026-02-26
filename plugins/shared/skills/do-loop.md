---
name: do-loop
description: Iteration protocol for agent team do-loops — guides how doer+critic teammate pairs communicate, iterate, and resolve disagreements
---

# Do-Loop Protocol

Reusable iteration pattern for doer+critic agent team loops. You (the team lead) spawn a pair of teammates, assign them a task, and they iterate until the critic approves or you intervene.

## 1. Team Spawning Protocol

Spawn exactly 2 teammates per loop — one doer, one critic.

For each teammate, describe:

| Field | What to include                                        |
|-------|--------------------------------------------------------|
| Role name | Short identifier (e.g. "implementer", "code-reviewer") |
| Responsibility | One sentence — what this teammate does                 |
| Model | `opus` for both doer and critic                        |
| Context | What the teammate needs to know to start working       |

Pass the critic a summary of the iteration rules (max 3 rounds, structured feedback format, exit conditions) so it knows the protocol.

**Example spawn description:**

> Start a team of 2:
> 1. **implementer** (opus) — Implements the authentication middleware for Express routes. Context: the route files are in `src/routes/`, existing middleware is in `src/middleware/auth.ts`, acceptance criteria are in the task below.
> 2. **code-reviewer** (opus) — Reviews the implementer's authentication middleware for security issues, correctness, and edge cases. Context: review protocol is max 3 rounds, use structured feedback (`file:line — issue — fix`), mark task complete when no issues above nit-level remain.

## 2. Task Structure

Create one task per loop using the shared team task list. Use the native task states:

| State | Who sets it | When |
|-------|------------|------|
| `pending` | You (lead) | Task created, waiting for doer to claim |
| `in_progress` | Doer | Doer claims the task and starts work |
| `completed` | Critic | Critic approves — no remaining issues above nit-level |

**Title format:** `[verb] [component] — [acceptance criteria summary]`

**Task description must include:**

- **Done means**: concrete definition of completion (e.g. "all routes require valid JWT, tests pass")
- **Files in scope**: which files/directories the doer should touch
- **Quality bar**: what the critic should enforce (e.g. "no security issues, error handling for all failure modes")

The doer claims the task (sets `in_progress`). The critic reviews when the doer signals readiness and marks `completed` when satisfied.

## 3. Communication Protocol

All communication is direct messages between the pair. No broadcasts.

**Critic to doer** — structured feedback:

```
file:line — issue description — suggested fix
```

Example:
```
src/middleware/auth.ts:24 — JWT expiry not checked — add `if (decoded.exp < Date.now() / 1000) throw new UnauthorizedError()`
```

**Doer to critic** — readiness signal with change summary:

```
Addressed feedback, ready for re-review:
- Added JWT expiry check (auth.ts:24-28)
- Added test for expired token case
```

Rules:
- No meta-discussion about process — keep messages focused on the work
- Doer sends "ready for review" when work is complete, not before
- Critic responds with either structured feedback or approval

## 4. Iteration Rules

Maximum **3 review rounds** per loop.

| Round | Scope | What the critic checks |
|-------|-------|----------------------|
| 1 | Full review | All issues — correctness, security, edge cases, style |
| 2 | Fixes only | Verify fixes are correct, flag new issues introduced by fixes |
| 3 | Final check | Nit-level only — naming, formatting, minor suggestions |

After round 3, if issues remain above nit-level, the critic escalates to you (the team lead) with a list of unresolved issues. You decide how to proceed.

## 5. Exit Conditions

**Normal exit:** The critic marks the task complete when no remaining issues above nit-level exist.

**Deadlock:** If the doer and critic disagree on the same issue for 2 consecutive rounds, escalate to you with both perspectives:

```
DEADLOCK on src/middleware/auth.ts:24
- Doer: "Expiry check belongs in the route handler, not middleware"
- Critic: "Middleware is the correct layer — route handlers shouldn't handle auth logic"
Action needed: pick a side or accept as-is
```

**Your override options:**
1. Accept as-is — move on without the change
2. Side with doer — critic drops the issue
3. Side with critic — doer implements the fix

## 6. Composability

You can chain multiple do-loops sequentially. Each loop is independent.

**Between loops:**
1. Summarize the completed loop's results
2. Spawn a new teammate pair for the next loop
3. Pass relevant output from the previous loop as context

Previous teammates are done after their loop — do not reuse them across loops. Each loop gets a fresh pair.

**Example — two chained loops:**

```
Loop 1: "Implement auth middleware — JWT validation, role checking, error responses"
  → implementer + code-reviewer
  → result: auth middleware complete, 3 rounds, all issues resolved

Loop 2: "Write integration tests for auth middleware — cover valid/invalid/expired tokens"
  → test-writer + test-reviewer
  → context: auth middleware from loop 1 is in src/middleware/auth.ts
  → result: 12 tests written, all passing
```

## Communication Flow Example

A complete loop from start to finish:

```
[You]       Create task: "Add rate limiting to /api/login — max 5 attempts per IP per minute" (pending)
[doer]      Claims task (in_progress), implements rate limiter in src/middleware/rate-limit.ts
[doer→critic]  "Ready for review — added rate limiting middleware and wired it to login route"

--- Round 1 ---
[critic→doer]  src/middleware/rate-limit.ts:15 — no cleanup of expired entries — add a TTL or periodic sweep
               src/middleware/rate-limit.ts:31 — returns 500 instead of 429 — change status code to 429
               src/routes/auth.ts:8 — middleware applied after body parsing — move before bodyParser

[doer→critic]  "Addressed feedback, ready for re-review:
                - Added TTL-based cleanup (rate-limit.ts:15-22)
                - Fixed status code to 429 (rate-limit.ts:31)
                - Moved middleware before bodyParser (auth.ts:6)"

--- Round 2 ---
[critic→doer]  src/middleware/rate-limit.ts:18 — TTL cleanup runs on every request — move to setInterval

[doer→critic]  "Addressed — moved to 60s interval with cleanup on process exit"

--- Round 3 ---
[critic]       No issues. Task marked complete (completed).
```
