# Quick Reference: Agent Schedules & Dependencies

## Agent Schedule Overview

| # | Agent | Time | Dependencies | Creates PRs? | Blocks |
|---|-------|------|--------------|--------------|--------|
| 1 | Conductor | 08:00 | None | No | All (coordination) |
| 2 | PM | 09:00 | None | No | Architect |
| 3 | Architect | 10:00 | PM | Maybe | Backend, Frontend |
| 4 | Backend | 11:00 | Architect | Yes | Frontend |
| 5 | Frontend | 12:00 | Backend | Yes | QA, Security, Perf, User-Tester |
| 6 | UX | 13:00 | Frontend (soft) | No | Frontend (feedback) |
| 7 | QA | 14:00 | Backend + Frontend | No | None |
| 8 | Security | 15:00 | All code | No | PRs (approval) |
| 9 | Performance | 16:00 | Backend + Frontend | No | None |
| 10 | Docs | 17:00 | All merged | No | None |
| 11 | Human Tasks | 18:00 | All | No | Agents (tickets) |
| 12 | Code Reviewer | 19:00 | All PRs | No | PRs (approval) |
| 13 | User Tester | 20:00 | Frontend | No | None |
| 14 | Optimizer | 21:00 | All logs | No | Agents (recommendations) |
| 15 | Conductor EOD | 22:00 | All | No | None |

## Critical Dependency Chains

### Chain 1: Foundation → Implementation
```
PM (09:00)
  ↓
Architect (10:00) [Must merge by 10:45]
  ↓
Backend (11:00) [Must merge by 11:45]
  ↓
Frontend (12:00) [Must merge by 13:45]
  ↓
QA/Security/Performance/User-Tester (14:00-20:00)
```

### Chain 2: Review → Approval → Merge
```
Code Reviewer (19:00) → Approves PRs → Auto-merge → Next day clear
```

## Mandatory Checks by Agent Type

### Implementation Agents (Backend, Frontend)

**Pre-Work Checklist:**
```bash
[ ] git checkout main && git pull origin main --rebase
[ ] Check dependency PR merged (git log or gh pr list)
[ ] If NOT merged: Create blocking ticket, exit
[ ] If merged: Re-read latest contracts/architecture
[ ] Proceed with implementation
```

**Post-Work Checklist:**
```bash
[ ] All tests pass locally
[ ] No merge conflicts with main
[ ] PR description includes dependencies
[ ] PR has auto-merge label/setting
[ ] Monitor PR for 15 minutes
```

### Validation Agents (QA, Security, Performance)

**Pre-Work Checklist:**
```bash
[ ] git checkout main && git pull origin main --rebase
[ ] Verify implementation PRs merged
[ ] If NOT merged: Block OR test previous version
[ ] If merged: Test/review latest code
[ ] Report findings with version info
```

### Coordination Agents (Conductor, PM, Architect)

**Pre-Work Checklist:**
```bash
[ ] Sync latest state
[ ] Check for blockers from previous day
[ ] Update coordination files
[ ] Create clear, actionable work for dependents
```

### Support Agents (Docs, Human Tasks, Optimizer, UX, User-Tester)

**Pre-Work Checklist:**
```bash
[ ] Sync latest state
[ ] Identify what's merged vs. in-progress
[ ] Document/coordinate merged work
[ ] Handle unmerged gracefully
[ ] Create forward-looking work
```

## Blocking Scenarios & Actions

### Scenario 1: Backend PR Not Merged (at 12:00)

**Who's Affected:** Frontend

**Frontend Actions:**
```bash
1. Detect: gh pr list --author backend --state open
2. Create: .jules/tickets/TICKET-FE-XXX-blocked-backend.md
3. Log: "Frontend BLOCKED - Backend PR #N not merged"
4. Exit: Skip implementation, try tomorrow
```

**Conductor Actions:**
```markdown
Update daily.md:
| 12:00 | frontend | UI integration | Backend PR #N | 🚫 BLOCKED |
```

### Scenario 2: Frontend PR Not Merged (at 14:00)

**Who's Affected:** QA, Security, Performance, User-Tester

**QA Actions:**
```bash
1. Detect: gh pr list --author frontend --state open
2. Create: .jules/tickets/TICKET-QA-XXX-blocked-frontend.md
3. Log: "QA BLOCKED - Frontend PR #M not merged"
4. Exit: Skip testing, try tomorrow
```

**Similar for Security, Performance, User-Tester**

### Scenario 3: Merge Conflicts Detected

**Who Detects:** Security (15:00), Human Tasks (18:00), Code Reviewer (19:00)

**Actions:**
```bash
Security (15:00):
  - Flag: CRITICAL security risk (conflicts can hide vulnerabilities)
  - Create: TICKET-SEC-XXX-merge-conflict-risk.md

Human Tasks (18:00):
  - Detect: gh pr list --state open --json mergeable
  - Create: HUMAN-XXX-merge-conflict.md (CRITICAL priority)
  - Specify: Files, likely cause, urgency

Code Reviewer (19:00):
  - Mark PR: 🚫 BLOCKED - Merge conflicts, cannot approve
  - Note: Which PR conflicts with, resolution steps
```

### Scenario 4: Auto-Merge Not Working

**Who Detects:** Human Tasks (18:00), Conductor EOD (22:00)

**Human Tasks Actions:**
```bash
1. Detect: gh pr list --state open (filter > 2 hours old)
2. Check: All checks passing? Reviews approved?
3. Create: HUMAN-XXX-pr-approval-needed.md
4. Specify: Which PR, blocking impact, urgency
```

**Conductor EOD Actions:**
```markdown
Note in daily.md:
- PR #N open for 3 hours, auto-merge not working
- Blocks: Frontend tomorrow (12:00)
- Action: Human investigation required
```

## Emergency Procedures

### Emergency 1: Critical PR Must Merge NOW

**Scenario:** Backend PR critical, Frontend already running

**Actions:**
```bash
Human:
  1. Manually merge Backend PR immediately
  2. git push origin main (force if needed)
  3. Notify Frontend agent to retry (if possible)

Conductor (next run):
  1. Note emergency merge in daily.md
  2. Verify system stabilized
  3. Review why normal process failed
```

### Emergency 2: Multiple Merge Conflicts

**Scenario:** 3+ PRs with merge conflicts

**Actions:**
```bash
Human:
  1. Assess: Which PR is foundation? (Architect > Backend > Frontend)
  2. Merge foundation first (resolve conflicts)
  3. Rebase other PRs on new main
  4. Guide agents to re-create PRs

Optimizer (21:00):
  1. Detect pattern: Multiple conflicts = sync issue
  2. Create recommendation: Strengthen git pull requirements
  3. Suggest: More frequent syncs or longer intervals
```

### Emergency 3: System-Wide Blocking

**Scenario:** 10+ agents blocked on same dependency

**Actions:**
```bash
Human:
  1. Identify root blocker (likely PM or Architect)
  2. Manually resolve blocker
  3. Clear blocking tickets
  4. Restart affected agents (if possible)

Conductor (next run):
  1. Post-mortem: Why did system-wide block occur?
  2. Document: Recovery steps taken
  3. Recommend: Workflow adjustments to prevent repeat
```

## Quick Diagnostics

### Check 1: Is Agent Blocked?

```bash
# Look in agent-runs.log
cat .jules/agent-runs.log | grep -i blocked | tail -5

# Check for blocking tickets
ls .jules/tickets/*-blocked.md 2>/dev/null
```

### Check 2: PR Merge Status

```bash
# All open PRs
gh pr list --state open

# PRs from today
gh pr list --search "created:>=$(date -I)"

# Check specific PR merge readiness
gh pr view <number> --json mergeable,mergeStateStatus
```

### Check 3: What's Blocking Tomorrow?

```bash
# Check Conductor's EOD summary
cat .jules/daily.md | grep -A 10 "Tomorrow's Blockers"

# Check open PRs that will block
gh pr list --state open --json number,author | \
  jq -r '.[] | "\(.author.login) PR #\(.number) will block dependents"'
```

### Check 4: System Health

```bash
# Conductor's latest status
cat .jules/daily.md | grep "System Health" -A 5

# Agent success rate today
cat .jules/agent-runs.log | grep "$(date -I)" | \
  grep -c "complete" vs grep -c "BLOCKED"

# PR merge performance
cat .jules/daily.md | grep "Merge Performance" -A 3
```

## Optimization Targets

### Excellent Performance

- ✅ PR merge latency: < 15 minutes average
- ✅ Auto-merge success: 98%+
- ✅ Agent blocking rate: < 5%
- ✅ Merge conflicts: 0 per week
- ✅ Human interventions: < 1 per week

### Good Performance

- ✅ PR merge latency: 15-30 minutes
- ✅ Auto-merge success: 90-98%
- ✅ Agent blocking rate: 5-15%
- ✅ Merge conflicts: 1-2 per week
- ✅ Human interventions: 1-3 per week

### Needs Improvement

- ⚠️ PR merge latency: 30-60 minutes
- ⚠️ Auto-merge success: 80-90%
- ⚠️ Agent blocking rate: 15-30%
- ⚠️ Merge conflicts: 3-5 per week
- ⚠️ Human interventions: 4-7 per week

### Critical Issues

- ❌ PR merge latency: > 60 minutes
- ❌ Auto-merge success: < 80%
- ❌ Agent blocking rate: > 30%
- ❌ Merge conflicts: > 5 per week
- ❌ Human interventions: > 7 per week

**Action:** If in "Critical Issues" range, review JULES-SCHEDULE-GUIDE.md for optimization strategies.
