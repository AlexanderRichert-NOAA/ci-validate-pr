# ci-validate-pr

This repository provides a reusable GitHub Actions workflow with several optional PR validations. All checks are **off by default** and enabled via inputs.

## Available checks

| Check | What it does |
|---|---|
| **AI disclosure** | Ensures the PR body contains a completed Generative AI usage declaration |
| **External contributor approvals** | Requires a minimum number of approvals when the PR author is not a repo owner or collaborator |
| **Critical change approvals** | Requires a minimum number of approvals when the PR modifies many files or specific critical files |

## Usage

Call this workflow from your own workflow using `workflow_call`:

```yaml
# .github/workflows/validate-pr.yml
name: Validate PR

on:
  pull_request:
    types: [opened, synchronize, reopened, review_requested]
  pull_request_review:
    types: [submitted]

jobs:
  validate:
    uses: NOAA-EMC/ci-validate-pr/.github/workflows/validate-pr.yaml@main
    with:
      validate-ai-disclosure: true
      approvals-for-external-contributors: 1
      approvals-for-critical-changes: 2
      critical-file-count: 10
      critical-files: |
        .github/workflows/deploy.yml
        src/import_file
```

> [!NOTE]
> Approval-based checks require `pull_request_review` as a trigger so the workflow re-runs when reviews are submitted.

## Inputs

| Input | Type | Default | Description |
|---|---|---|---|
| `validate-ai-disclosure` | `boolean` | `false` | If true, runs the AI disclosure validation check on the PR body |
| `approvals-for-external-contributors` | `number` | `0` (disabled) | Required number of approvers for PRs from external contributors. |
| `approvals-for-critical-changes` | `number` | `0` (disabled) | Required number of approvers for PRs that modify many files (>='critical-file-count') or critical files ('critical-files'). |
| `critical-file-count` | `number` | `10` | Minimum number of modified files to trigger the minimum approver requirement ('approvals-for-critical-changes'). |
| `critical-files` | `string` | `''` | Newline-separated list of files that, if modified, trigger the minimum approver requirement ('approvals-for-critical-changes'). |