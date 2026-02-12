# Agent System - Rollback & Conflict Resolution Guide

## 🔄 Rollback Instructions

### When to Rollback

**Critical Issues (Rollback Immediately):**
- Breaking changes that crash the site
- Security vulnerabilities introduced
- Data loss or corruption
- Conflicts preventing any agent from working

**Non-Critical (Fix Forward):**
- Minor bugs
- Style inconsistencies
- Performance issues
- Content typos

### How to Rollback

#### Option 1: Revert Last Commit (Safest)
```bash
# See what changed
git log --oneline -5

# Revert last commit (creates new commit, preserves history)
git revert HEAD

# If multiple commits need reverting
git revert HEAD~2..HEAD

# Push the revert
git push origin $(git branch --show-current)
```

#### Option 2: Reset to Last Known Good (Nuclear Option)
```bash
# ONLY use this if revert doesn't work
# Get last known good SHA from .jules/recovery/last-known-good.sha
LAST_GOOD=$(cat .jules/recovery/last-known-good.sha)

# Reset to that point
git reset --hard $LAST_GOOD

# Force push (DANGEROUS - coordinate with team)
git push --force-with-lease origin $(git branch --show-current)
```

#### Option 3: Create Recovery Branch
```bash
# Safest approach - create branch from last good state
LAST_GOOD=$(cat .jules/recovery/last-known-good.sha)
git checkout -b recovery-$(date +%Y%m%d) $LAST_GOOD

# Fix issues on this branch
# Then merge back when ready
```

### Rollback Checklist

- [ ] Document why rollback happened in `.jules/log.md`
- [ ] Notify all agents in `.jules/schedule.md` (mark as "RECOVERY MODE")
- [ ] Create `.jules/tickets/ROLLBACK-[date].md` with:
  - What broke
  - Why it broke
  - How it was fixed
  - Prevention for future
- [ ] Update `.jules/recovery/last-known-good.sha` after successful fix

## ⚔️ Conflict Resolution

### Types of Conflicts

#### 1. Code Conflicts (Git Merge Conflicts)
**When:** Two agents edit same file

**Resolution:**
```bash
# 1. Identify conflicting files
git status

# 2. See the conflict
cat conflicting-file.tsx
# Look for <<<<<<< HEAD, =======, >>>>>>> markers

# 3. Decide which version to keep (or merge manually)
# Option A: Keep theirs
 git checkout --theirs conflicting-file.tsx
 
# Option B: Keep ours
git checkout --ours conflicting-file.tsx

# Option C: Manual merge (edit file, remove markers)
# Choose best parts from both versions

# 4. Mark as resolved
git add conflicting-file.tsx
git commit -m "Resolved merge conflict in [file]"
```

#### 2. Architecture Conflicts
**When:** Tech Lead and Backend disagree on approach

**Resolution Process:**
1. **STOP** - Don't proceed with conflicting implementations
2. **Document** - Each agent writes their approach in `.jules/tickets/CONFLICT-[topic].md`
3. **Compare** - Create comparison table:
   ```markdown
   | Approach | Pros | Cons | Risk | Effort |
   |----------|------|------|------|--------|
   | A | ... | ... | ... | ... |
   | B | ... | ... | ... | ... |
   ```
4. **Decide** - Use decision matrix:
   - Scalability: /10
   - Maintainability: /10
   - Performance: /10
   - Effort: /10
   - Total score decides
5. **Record** - Document decision in `.jules/architecture/decisions.md`

#### 3. Design Conflicts
**When:** Creative Director and UX Architect disagree on design

**Resolution Process:**
1. **Reference strategy** - Check `.jules/strategy/product-vision.md`
2. **User test** - Which approach better serves user personas?
3. **Prototype both** - Quick mockups of each approach
4. **Decision criteria:**
   - User goal alignment
   - Accessibility
   - Brand consistency
   - Technical feasibility
5. **Document** - Record in `.jules/design/decisions.md`

#### 4. Content Conflicts
**When:** Content Strategist and Product Strategist disagree on messaging

**Resolution:**
1. Check user personas - what resonates with them?
2. Check business goals - what drives conversions?
3. A/B test if possible (deploy both, measure)
4. Otherwise, Product Strategist has final say on messaging

### Conflict Prevention

#### Before Starting Work
- **Read all dependencies** - Check what other agents have done
- **Check for open PRs** - See if related work is in progress
- **Look for BLOCKED tickets** - See if there are known issues

#### During Work
- **Communicate in files** - Write comments explaining your decisions
- **Update schedule** - Mark what you're working on
- **Small commits** - Easier to revert if conflicts arise

#### After Work
- **Document decisions** - Why did you choose this approach?
- **Create tickets** - Flag potential issues for next agents
- **Review conflicts** - Check if your work conflicts with others

### Conflict Escalation

**Level 1 - Self-Resolve:**
Agent identifies conflict, proposes solution in ticket

**Level 2 - Agent Discussion:**
Conflicting agents discuss in `.jules/tickets/CONFLICT-[topic].md`

**Level 3 - Conductor Intervention:**
If agents can't agree, Conductor reviews and decides

**Level 4 - Human Override:**
If system can't resolve, create `HUMAN-OVERRIDE-[topic].md` for human decision

## 🆘 Emergency Procedures

### Complete System Failure
```bash
# If everything is broken and agents are confused

# 1. Stop all work
echo "SYSTEM HALT - See .jules/tickets/SYSTEM-HALT.md" > .jules/STATUS

# 2. Create halt ticket
cat > .jules/tickets/SYSTEM-HALT.md << 'EOF'
# SYSTEM HALT
## Time: [timestamp]
## Reason: [what went wrong]
## Impact: [which agents affected]

### Immediate Actions Required
- [ ] Stop all agent execution
- [ ] Assess damage
- [ ] Determine rollback point
- [ ] Fix root cause
- [ ] Resume operation

### Recovery Plan
[Step-by-step recovery process]
EOF

# 3. Notify all agents via schedule update
# 4. Human intervention required
```

### Lost Branch/Work
```bash
# If Jules loses the branch or work disappears

# Find recent commits
git reflog

# Recover from reflog
git checkout -b recovery-branch HEAD@{2}

# Or recover specific file from history
git checkout HEAD~1 -- path/to/file
```

## 📋 Daily Safety Checks

**Conductor (Start of Day):**
- [ ] Check `.jules/recovery/last-known-good.sha` exists
- [ ] Verify all agents from yesterday completed
- [ ] Check for open conflicts from yesterday
- [ ] Review `.jules/log.md` for issues

**Conductor (End of Day):**
- [ ] Update `.jules/recovery/last-known-good.sha` with current SHA
- [ ] Document any rollbacks or conflicts
- [ ] Create recovery plan for next day

## 🎯 Best Practices

1. **Commit early, commit often** - Smaller commits = easier rollback
2. **Write clear commit messages** - "Fix navigation bug" not "update"
3. **Document decisions** - Why did you choose this approach?
4. **Test before pushing** - Don't break main for others
5. **Communicate in files** - Not in chat, in actual files
6. **When in doubt, block** - Better to wait than create conflicts

## Files to Check

When resolving conflicts, read these:
- `.jules/log.md` - What happened?
- `.jules/schedule.md` - Who was supposed to do what?
- `.jules/tickets/BLOCKED-*.md` - What was blocked?
- `.jules/tickets/CONFLICT-*.md` - Documented conflicts
- Git log - What changed?

## Remember

**Better to block than to conflict.**
**Better to rollback than to ship broken code.**
**Better to ask in a ticket than in chat.**
