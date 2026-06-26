# ci-validate-pr

This repository provides a reusable GitHub Actions workflow with several optional PR validations. All checks are **off by default** and enabled via inputs.

## Available checks

| Check | What it does |
|---|---|
| **GenAI disclosure** | Ensures the PR body contains a completed GenAI usage declaration |
| **External contributor approvals** | Requires a minimum number of approvals when the PR author is not a repo owner or collaborator |
| **Critical change approvals** | Requires a minimum number of approvals when the PR modifies many files or specific critical files |

## Usage

Call this workflow from your own workflow:

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
    uses: NOAA-EMC/ci-validate-pr/.github/workflows/validate-pr.yaml@93efa0c40935a575689fc640acd46e845a45f679
    with:
      validate-genai-disclosure: true
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
| `validate-genai-disclosure` | `boolean` | `false` | If true, runs the GenAI disclosure validation check on the PR body |
| `approvals-for-external-contributors` | `number` | `0` (disabled) | Required number of approvers for PRs from external contributors. |
| `approvals-for-critical-changes` | `number` | `0` (disabled) | Required number of approvers for PRs that modify many files (>='critical-file-count') or critical files ('critical-files'). |
| `critical-file-count` | `number` | `10` | Minimum number of modified files to trigger the minimum approver requirement ('approvals-for-critical-changes'). |
| `critical-files` | `string` | `''` | Newline-separated list of files that, if modified, trigger the minimum approver requirement ('approvals-for-critical-changes'). |


_Generative AI tools are used to assist with developing this code._
_The code has been reviewed, edited, and validated by NWS staff._
