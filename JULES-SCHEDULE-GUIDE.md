# Jules Schedule System - Agent Coordination Guide

## Overview

This repository contains 15 specialized AI agents designed to work together in **Jules by Google's scheduled agent system**. Each agent runs at a fixed 1-hour interval, creating a coordinated workflow for enterprise-level software development.

## The Challenge: Sequential PR Dependencies

### Problem We Solved

When agents run on fixed schedules (7am, 8am, 9am, etc.), they create Pull Requests that must be merged before dependent agents can work on them. The original issue:

1. **Agent 1** (7am): Creates PR with new feature
2. **PR not merged**: Either waiting for auto-merge checks or human approval
3. **Agent 2** (8am): Starts working on old codebase (doesn't have Agent 1's changes)
4. **Agent 1's PR merges** (later)
5. **Agent 2 creates PR**: Now has merge conflicts with Agent 1's changes
6. **Result**: Wasted work, merge conflicts, manual intervention required

### Our Solution: Multi-Layer Protection

We implemented a comprehensive solution across all 15 agents:

## 1. Mandatory Synchronization Protocol

**Every implementation agent MUST:**
```bash
# Before ANY work
git checkout main
git pull origin main --rebase

# Verify dependencies merged
git log --since="today" --author="<dependent-agent>" --oneline
```

**Why:** Prevents working on stale code that will cause merge conflicts later.

## 2. Explicit Dependency Blocking

**Agents verify dependencies and self-block if not met:**
```markdown
If Backend PR not merged:
  1. Create .jules/tickets/TICKET-FE-XXX-blocked.md
  2. Log blockage in agent-runs.log
  3. Exit without implementing (prevents wasted work)
  4. Try again tomorrow when dependency met
```

**Why:** Better to skip work than create conflicting PRs.

## 3. Conductor PR Merge Tracking

**Conductor (Agent 1) tracks all PR merge status:**
```markdown
| PR# | Agent | Created | Merged | Latency | Status | Blocks |
|-----|-------|---------|--------|---------|--------|--------|
| #42 | Backend | 11:05 | 11:18 | 13 min | ✅ MERGED | None |
| #43 | Frontend | 12:07 | — | — | ⏳ PENDING | QA, Security, Perf |
```

**Why:** Visibility into system health and bottlenecks.

## 4. Auto-Merge Optimized PRs

**All PRs are created with auto-merge in mind:**
- ✅ All tests pass before PR creation
- ✅ No merge conflicts
- ✅ Clear dependency declarations
- ✅ Auto-merge enabled
- ✅ Target merge time: < 30 minutes

**Why:** Fast merges prevent blocking downstream agents.

## Agent Schedule & Dependencies

### The 15-Agent Schedule

| Time | Agent | Role | Dependencies | Blocks |
|------|-------|------|--------------|--------|
| 08:00 | **Conductor** | Orchestration | None | All |
| 09:00 | **PM** | Product Strategy | None | Architect |
| 10:00 | **Architect** | System Design | PM | Backend, Frontend |
| 11:00 | **Backend** | API Implementation | Architect | Frontend |
| 12:00 | **Frontend** | UI Implementation | Backend | QA, Security, Perf, User-Tester |
| 13:00 | **UX** | Design Review | Frontend (flexible) | Frontend (feedback) |
| 14:00 | **QA** | Testing | Backend + Frontend | None |
| 15:00 | **Security** | Security Review | All code | None |
| 16:00 | **Performance** | Optimization | Backend + Frontend | None |
| 17:00 | **Docs** | Documentation | All merged changes | None |
| 18:00 | **Human Tasks** | Coordination | All | Agents (via tickets) |
| 19:00 | **Code Reviewer** | Quality Review | All PRs | Approval to merge |
| 20:00 | **User Tester** | Usability | Frontend | None |
| 21:00 | **Optimizer** | System Improvement | All logs | Agents (via recommendations) |
| 22:00 | **Conductor (EOD)** | Daily Wrap | All | None |

### Critical Dependency Chains

**Chain 1: Architecture → Implementation → Validation**
```
PM (9am) → Architect (10am) → Backend (11am) → Frontend (12pm) → QA/Security/Perf (2-4pm)
```
- **Critical Point**: If Architect's PR not merged by 10:45am, Backend is blocked
- **Critical Point**: If Backend's PR not merged by 11:45am, Frontend is blocked
- **Critical Point**: If Frontend's PR not merged by 1:45pm, QA/Security/Perf are blocked

**Chain 2: Review → Approval → Next Day**
```
Code Reviewer (7pm) → [Auto-Merge Overnight] → Next Day's Agents (9am+)
```
- **Critical Point**: If PRs not approved by 7pm, they won't auto-merge, blocking next day

## How Each Agent Handles Dependencies

### Implementation Agents (Backend, Frontend)

**Before starting work:**
1. ✅ Pull latest main
2. ✅ Verify dependency PR merged
3. ❌ If not merged: Self-block, create ticket, exit
4. ✅ If merged: Proceed with implementation
5. ✅ Create auto-merge ready PR
6. ✅ Monitor PR for quick merge

### Validation Agents (QA, Security, Performance)

**Before testing/reviewing:**
1. ✅ Pull latest main
2. ✅ Verify implementation PRs merged
3. ❌ If not merged: Test previous version OR self-block
4. ✅ If merged: Test/review latest code
5. ✅ Report findings

### Coordination Agents (Conductor, PM, Architect)

**Daily routine:**
1. ✅ Sync latest state
2. ✅ Check for blockers
3. ✅ Update coordination files
4. ✅ Create work for dependent agents

### Support Agents (Docs, Human Tasks, Optimizer)

**Flexible approach:**
1. ✅ Sync latest state
2. ✅ Document/coordinate merged changes
3. ✅ Handle unmerged PRs gracefully
4. ✅ Create forward-looking work

## Failure Modes & Recovery

### Scenario 1: Auto-Merge Doesn't Work

**Symptoms:** PR sits open for > 30 minutes with passing checks

**Detection:**
- Human Tasks agent (6pm) detects stale PRs
- Creates HUMAN-XXX-pr-approval-needed.md
- Escalates to human for investigation

**Recovery:**
- Human checks GitHub settings (auto-merge enabled?)
- Human checks branch protection (requires manual approval?)
- Human manually merges or fixes settings

### Scenario 2: Merge Conflicts

**Symptoms:** PR has conflicts with main

**Detection:**
- Security agent (3pm) flags conflicts as CRITICAL
- Human Tasks agent (6pm) creates merge conflict ticket
- Conductor (10pm) notes tomorrow's blockers

**Recovery:**
- Human resolves conflicts manually
- Agent re-creates PR with latest main
- Dependent agents blocked until resolution

### Scenario 3: Dependent Agent Runs Before Dependency Merged

**Symptoms:** Frontend starts at 12pm, but Backend PR from 11am not merged yet

**Detection:**
- Frontend's mandatory dependency check (Phase 1)
- Detects Backend PR still open
- Self-blocks immediately

**Recovery:**
- Frontend creates blocking ticket
- Skips work for the day
- Tries again tomorrow
- No merge conflicts created!

### Scenario 4: CI Checks Take Too Long

**Symptoms:** PR created but checks take > 30 minutes

**Detection:**
- Optimizer (9pm) analyzes PR merge latency
- Detects pattern of slow CI
- Creates performance recommendation

**Recovery:**
- Human optimizes CI (parallel tests, caching, etc.)
- Reduces check time to < 15 minutes
- PRs merge faster, blocking reduced

## Best Practices for Jules Schedule

### 1. Always Enable Auto-Merge on Repository

```bash
# GitHub Settings → Branches → Branch protection rules
✅ Require pull request reviews before merging: 0 reviewers (for agents)
✅ Require status checks to pass: true
✅ Require branches to be up to date: true
✅ Allow auto-merge: true
```

### 2. Keep CI Checks Fast

**Target: All checks complete in < 15 minutes**
- Parallelize tests
- Cache dependencies
- Split into fast/slow suites
- Run critical checks first

### 3. Monitor PR Merge Latency Daily

**Use Conductor's end-of-day report:**
```markdown
Average merge time: 18 minutes ✅ (target: < 30 min)
Auto-merge success rate: 93% ⚠️ (target: 100%)
PRs requiring human intervention: 1 (target: 0)
```

### 4. Review Optimizer's Bottleneck Analysis Weekly

**Look for patterns:**
- Which agents frequently blocked?
- Which PRs take longest to merge?
- What systemic issues exist?
- What timing adjustments needed?

### 5. Handle Human Tasks Promptly

**Check at end of day:**
```bash
ls .jules/human-tasks/*.md | grep CRITICAL
```
- Critical tasks block next day's work
- PR approvals needed urgently
- Merge conflicts need resolution

## Debugging Guide

### Check 1: Agent Logs

```bash
# See what each agent did today
cat .jules/agent-runs.log | grep "$(date -I)"

# Look for BLOCKED status
cat .jules/agent-runs.log | grep BLOCKED
```

### Check 2: PR Status

```bash
# See all open PRs
gh pr list --state open

# Check PR merge readiness
gh pr view <number> --json mergeable,mergeStateStatus,reviews,statusCheckRollup
```

### Check 3: Conductor's Daily Plan

```bash
# See today's plan and blockers
cat .jules/daily.md

# Check PR dependency chain
cat .jules/daily.md | grep -A 20 "PR Dependency Chain"
```

### Check 4: Blocking Tickets

```bash
# Find all blocking tickets
find .jules/tickets -name "*-blocked.md"

# Read a specific blocker
cat .jules/tickets/TICKET-FE-042-blocked.md
```

## Success Metrics

### Healthy Jules Schedule System

✅ **PR Merge Latency**: < 30 minutes average
✅ **Auto-Merge Success**: 95%+ of PRs merge without human intervention
✅ **Agent Blocking Rate**: < 10% of agents blocked per day
✅ **Merge Conflicts**: < 1 per week
✅ **Carry-Over Work**: < 20% of agents defer work to next day

### Unhealthy System (Needs Optimization)

❌ **PR Merge Latency**: > 1 hour average → Optimize CI
❌ **Auto-Merge Success**: < 80% → Fix GitHub settings or add manual approver
❌ **Agent Blocking Rate**: > 30% → Timing adjustments needed
❌ **Merge Conflicts**: > 1 per day → Strengthen sync protocols
❌ **Carry-Over Work**: > 50% → Review agent prompts, simplify work

## Advanced: Timing Optimization

### Scenario: Reduce Blocking Risk

**Problem:** Backend (11am) → Frontend (12pm) = only 1-hour buffer for PR to merge

**Solution Options:**

**Option 1: Add buffer time**
```
Backend: 11:00
Frontend: 12:30 (+30 min buffer)
```

**Option 2: Split Backend work**
```
Backend-Contracts: 11:00 (just API contracts)
Backend-Implementation: 11:30 (implementation)
Frontend: 13:00 (more time for merge)
```

**Option 3: Faster CI**
```
Optimize CI to < 10 minutes
PR merges by 11:15
Frontend at 12:00 has 45-min buffer
```

### Scenario: High-Priority vs. Low-Priority Work

**Problem:** All agents on same schedule, low-priority work delays high-priority

**Solution: Priority-based scheduling**
```markdown
High Priority (P0):
  Backend (11:00) → Frontend (12:00) → QA (14:00) → Deploy

Low Priority (P2):
  Docs (17:00) → Optimizer (21:00)
  (Can slip to next day without blocking)
```

## Conclusion

This Jules schedule system represents an enterprise-grade approach to AI agent coordination:

✅ **Fault-tolerant**: Agents self-block rather than create conflicts
✅ **Auditable**: All decisions logged, all blockages documented
✅ **Self-optimizing**: System detects bottlenecks and recommends improvements
✅ **Human-friendly**: Escalates issues that need human intervention
✅ **Scale-ready**: Supports 15 agents × 24-hour cycle = high throughput

**The key insight:** In a scheduled system, prevention is better than resolution. Agents that detect issues and self-block are more valuable than agents that blindly proceed and create problems for others.
