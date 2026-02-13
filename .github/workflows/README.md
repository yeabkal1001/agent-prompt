# Auto-Merge Setup for Jules

## The Problem

Jules creates PRs but they don't auto-merge because:
1. Jules works in cloud environment
2. Creates branch and PR
3. Auto-merge is "hidden" or not triggered
4. Next agent works on old code (main branch)
5. Agents work on stale code

## The Solution

GitHub Actions that automatically merge agent PRs immediately.

## Setup Instructions

### 1. Enable GitHub Actions

Your repo already has the workflow files in `.github/workflows/`.

### 2. Give Actions Permissions

Go to your repo → Settings → Actions → General

Set:
- **Workflow permissions**: Read and write permissions
- ✅ Allow GitHub Actions to create and approve pull requests

### 3. That's It!

The workflows will now:
- Detect when Jules creates a PR
- Merge it immediately using squash
- Update merge status in `.jules/`

## How It Works

When an agent creates a PR:

1. **GitHub Action triggers** on `pull_request: opened`
2. **Waits 5 seconds** for any checks to start
3. **Attempts merge** using squash method
4. **Creates status file** `.jules/last-merge.json` with merge info
5. **If fails**: Creates `.jules/merge-failed.json` and comments on PR

## Workflow Files

### Option 1: Simple Auto-Merge (`immediate-merge.yml`)
- Most aggressive
- Merges immediately
- Minimal checks
- **Use this if you want speed**

### Option 2: Smart Auto-Merge (`auto-merge.yml`)
- Checks for conflicts first
- Waits for status checks
- More careful
- **Use this if you have CI checks**

### Option 3: Agent Merge Bot (`agent-merge-bot.yml`)
- Creates merge status files
- Agents can check `.jules/last-merge.json`
- Tracks all merges
- **Use this if agents need to know merge status**

## Agent Integration

Update your agents to check merge status:

```bash
# Check if previous agent's PR was merged
echo "Checking merge status..."

if [ -f ".jules/last-merge.json" ]; then
    LAST_MERGE=$(cat .jules/last-merge.json | grep -o '"author": "[^"]*"' | cut -d'"' -f4)
    echo "✅ Last merge by: $LAST_MERGE"
fi

# Or use git to check
PREV_AGENT="frontend"
MERGED_COMMITS=$(git log --since="1 hour ago" --author=".*" --oneline | wc -l)
if [ $MERGED_COMMITS -eq 0 ]; then
    echo "⚠️ No recent merges - waiting..."
    sleep 30
fi
```

## Troubleshooting

### PR Not Merging?

Check GitHub Action logs:
1. Go to Actions tab
2. Click on failed workflow
3. Read the error

Common issues:
- **Branch protection rules**: Disable "Require pull request reviews"
- **No write permissions**: Check workflow permissions in Settings
- **Merge conflicts**: Action will comment on PR

### Agents Still Work on Old Code?

Add this to agent dependency checks:

```bash
# Pull latest before checking
 git pull origin main --rebase

# Verify we're on latest
git log --oneline -1
```

### Want to Disable?

Delete or rename the workflow files:
```bash
rm .github/workflows/auto-merge.yml
rm .github/workflows/immediate-merge.yml
rm .github/workflows/agent-merge-bot.yml
```

## Best Practice

**Use Option 1 (immediate-merge.yml)** for fastest results:

1. Simple and fast
2. No complex checks
3. Works with sequential agent workflow
4. Minimal failure points

## Security Note

⚠️ **This auto-merges ALL PRs**, not just agent PRs.

If you want to limit to only agent PRs, modify the workflow:

```yaml
jobs:
  merge:
    if: contains(github.event.pull_request.title, 'Agent-') || contains(github.event.pull_request.user.login, 'jules')
    runs-on: ubuntu-latest
```

## Testing

Test the auto-merge:
1. Create a test PR manually
2. Watch Actions tab
3. Check if it merges automatically
4. If yes → Agents will work!

## Monitoring

Check merge history:
```bash
# See last 10 merges
git log --merges --oneline -10

# See today's merges
git log --since="today" --merges --oneline
```

## Success!

Once set up:
- ✅ Agent creates PR → Auto-merged in ~10 seconds
- ✅ Next agent pulls latest code
- ✅ No more stale code issues
- ✅ Smooth agent handoffs
