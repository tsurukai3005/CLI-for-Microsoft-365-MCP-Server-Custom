---
allowed-tools: TodoWrite, Bash, Read, Write
description: Generates a weekly PR activity summary and saves it to .tmp/
---

# PR Weekly Summary Generator

Generate a weekly activity summary of PRs on the current repository for the current week (Monday through today).

Usage: `/pr-summary`

**Output language**: Japanese (the generated report must be written in Japanese)

## Step 1: Detect Repository Info

```bash
# Get current repo info
REPO=$(gh repo view --json nameWithOwner --jq '.nameWithOwner')
AUTHOR=$(gh api user --jq '.login')
```

## Step 2: Fetch PR Data (Single Command)

Calculate the Monday of the current week, then fetch all PR information (including body) in **a single `gh` command**.

```bash
# Calculate Monday of the current week (if today is Monday, use today)
DOW=$(date +%u)
MONDAY=$(date -d "-$((DOW - 1)) days" +%Y-%m-%d)

# Fetch all PR info in one call (body contains the PR description)
gh pr list \
  --repo $REPO \
  --author $AUTHOR \
  --state all \
  --search "created:>=$MONDAY" \
  --json number,title,createdAt,mergedAt,state,body,url \
  --jq '.'
```

**Important**:
- `--json body` retrieves the PR description. There is no need to call `gh pr view` individually for each PR.
- After fetching, **filter out PRs where `state == "CLOSED"` and `mergedAt == null`** (closed without merge). These are abandoned/superseded PRs and must be excluded from the report entirely.

## Step 3: Fetch Commit Timestamps for Each PR

For each PR obtained in Step 2, fetch the commit history to determine the work duration.

```bash
# For each PR number ($PR_NUM) from Step 2
gh api repos/$REPO/pulls/$PR_NUM/commits \
  --jq '[.[].commit.committer.date] | sort | {first: first, last: last}'
```

From the commit timestamps, calculate:
- **作業開始**: The timestamp of the first commit
- **作業終了**: The timestamp of the last commit (or `mergedAt` if it is later than the last commit)
- **作業時間**: The difference between 作業開始 and 作業終了 in hours (round to nearest integer)

## Step 4: Generate Report

Using the fetched JSON data and commit timestamps, generate `.tmp/YYYY-WNN/MM-DD_HHmm_pr-summary.md` (ISO week subfolder + date-time prefix per core.md conventions). Create the week subfolder with `mkdir -p` if it does not exist.

### Document Structure

The output markdown must follow the same structure as the original pr-summary command from the source repository.

### Additional Rules

- **Language**: ALL content in the generated report file MUST be written in Japanese.
- **PR links**: Each detail section heading MUST be a markdown link using the `url` field.
- **Sort order**: Chronological (oldest first)
- **Status labels**: `[MERGED]`, `[OPEN]`
- **Closed-without-merge PRs**: Completely exclude from the report.
- **No horizontal rules**: Do NOT use `---` anywhere in the generated report.

## Step 5: Verify Output

Read the generated file using the Read tool and confirm the content is correct.

## Error Handling

- **No PRs found**: Report to user that no PRs were found for the current week. Do NOT generate a file.
- **Empty PR body**: Write "（PR説明文なし）" for the 目的 section.
- **gh command failure**: Report the error message and suggest the user check `gh auth status`.
