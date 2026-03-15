---
description: Analyze quiz and training performance across sessions. Shows domain strengths, weak task statements, scenario coverage, and recommends a targeted study path.
allowed-tools: Read, Glob, Grep, Bash
---

# Analysis Command

Reads your quiz and training history, computes performance metrics, and generates a personalized study plan.

## Usage

/analysis                # Full performance analysis
/analysis --domain 1     # Deep dive into Domain 1 performance
/analysis --history      # Show all quiz session results

## Workflow

### Step 1: Load Data

Read the study-path skill: `plugins/certification/skills/study-path.md`
Read the scoring skill: `plugins/certification/skills/scoring.md`

Read quiz history:
```
cat ~/.claude/certification/results.jsonl 2>/dev/null
```

Read training history:
```
cat ~/.claude/certification/training-progress.jsonl 2>/dev/null
```

If no data exists, display the following and exit:

> ## No Quiz History Found
>
> You haven't taken any quizzes yet. Start with:
> - `/training` — Study a domain interactively
> - `/quiz` — Take a full mock exam
>
> Then come back to `/analysis` to track your progress.

### Step 2: Compute Metrics

Follow the study-path skill's metrics computation:

1. **Per-domain accuracy** across all sessions
2. **Per-task-statement accuracy** across all sessions
3. **Weighted gap** = domain_weight x (1 - accuracy)
4. **Trend** = last 3 sessions vs all-time average (improving / plateau / regressing)
5. **Scenario exposure** = count of sessions per scenario
6. **Training coverage** = which task statements have been studied via `/training`

### Step 3: Generate Report

If the `--history` flag is present, show a table of all quiz sessions first:

#### Quiz History

| Date | Mode | Score | Pass? | Strongest | Weakest |
|------|------|-------|-------|-----------|---------|
| [one row per session from results.jsonl] | | | | | |

Then produce the full analysis regardless of flags:

#### Overall Readiness

- **Average scaled score:** [N] / 1000
- **Pass rate:** [N] / [total] sessions ([%])
- **Trend:** [Improving / Plateau / Regressing]

#### Domain Performance

| Domain | Weight | Accuracy | Weighted Gap | Trend | Status |
|--------|--------|----------|--------------|-------|--------|
| 1. Agentic Architecture | 27% | [%] | [N] | [arrow] | [status] |
| 2. Tool Design & MCP | 18% | [%] | [N] | [arrow] | [status] |
| 3. Claude Code Config | 20% | [%] | [N] | [arrow] | [status] |
| 4. Prompt Engineering | 20% | [%] | [N] | [arrow] | [status] |
| 5. Context Management | 15% | [%] | [N] | [arrow] | [status] |

Status thresholds: **Strong** (>80%) | **Needs Work** (60-80%) | **Critical** (<60%)

#### Critical Gaps (Task Statements Below 60%)

| Task Statement | Accuracy | Questions Seen | Domain |
|----------------|----------|----------------|--------|
| [sorted by weighted impact, highest first] | | | |

#### Scenario Exposure

| Scenario | Times Tested | Status |
|----------|-------------|--------|
| [flag scenarios with fewer than 2 exposures as under-tested] | | |

#### Training Coverage

| Domain | Task Statements Studied | Total | Coverage |
|--------|------------------------|-------|----------|
| [computed from training-progress.jsonl] | | | |

If the `--domain N` flag is present, add a deep-dive section for the specified domain showing:
- Per-task-statement accuracy breakdown
- Most-missed question IDs
- Comparison of training vs quiz performance on each task statement

### Step 4: Recommendations

Follow the study-path skill's recommendation algorithm to produce:

#### Recommended Study Path

**Immediate Priority**
1. [Highest weighted-gap domain with specific task statements to focus on]
2. [Second highest weighted-gap domain with specific task statements]

**Under-Tested Areas**
- [Scenarios with fewer than 2 exposures]
- [Task statements that have never appeared in a quiz]

**Suggested Actions**
- `/training [N]` — [reason based on weakest domain]
- `/quiz --domain [N] --hard` — [reason based on domains near the pass threshold]
- `/quiz --scenario [N]` — [reason based on under-tested scenarios]

**Readiness Assessment**

If scores indicate readiness: "Your scores suggest you are prepared. Consider one more full simulation to confirm consistency."

If scores indicate gaps remain: "Focus on the priority areas listed above. Target above 80% accuracy in all domains before attempting the exam."
