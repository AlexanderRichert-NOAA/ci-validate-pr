# ci-validate-pr

This repository provides a collection of reusable GitHub Actions workflows for pull request validation. Individual workflows can be used selectively, or use a single composite workflow to run multiple checks. The former is recommended for most cases, otherwise the unused ones will be listed as "skipped" under each pull request's checks. The combined workflow is provided in `validate-pr.yaml`, and is customizable using the same arguments as shown below.

## Validate GenAI disclosure: `validate-genai-disclosure.yaml`

This reusable workflow checks the body of a pull request to ensure that a GenAI usage declaration has been provided in order to comply with NOAA/NWS GenAI usage policy. It expects specific text in the PR body (it is recommended this be provided via PR template):
```markdown
### Generative AI disclosure (required)
- [ ] **[Insert AI tool name]** was used to assist with developing this code. The code has been reviewed, edited, and validated by NWS staff.
- [ ] No generative AI tools were used to develop any of the code in this PR.
```
where one and only one of the two boxes should be checked in the edited PR body, as well as the AI tool name inserted where shown.

### Usage
Add the following file to each repository where this check should be performed.
```yaml
# .github/workflows/validate-genai-disclosure.yaml
name: Validate GenAI disclosure

on:
  pull_request:
    types: [opened, synchronize, reopened, edited]

jobs:
  validate-genai-disclosure:
    uses: NOAA-EMC/ci-validate-pr/validate-genai-disclosure.yaml@a3c7e3ee61dc1c6f879a59d82d1f48e1e9f9fffa
```

## Validate critical change approvals: `validate-critical-change-approvals.yaml`

This reusable workflow requires a minimum number of approvals for pull requests that are considered "critical."
A change is considered critical if
- it modifies a number of files equal to or greater than a set value; _or_
- it modifies one or more files designated as critical.

### Usage
Add the following file to each repository where this check should be performed.
```yaml
# .github/workflows/validate-critical-change-approvals.yaml
name: Validate critical change approvals

on:
  pull_request_review:
    types: [submitted]

jobs:
  validate-critical-approvals:
    uses: NOAA-EMC/ci-validate-pr/validate-critical-change-approvals.yaml@a3c7e3ee61dc1c6f879a59d82d1f48e1e9f9fffa
    with:
      approvals-for-critical-changes: 2
      critical-file-count: 20
      critical-files: |
        src/core/important-file.js
        package.json
        .github/workflows/main.yml
```

### Inputs

| Input | Type | Default | Description |
|---|---|---|---|
| `approvals-for-critical-changes` | `number` | `0` | Required number of approvers for PRs that modify many files ('critical-file-count') or critical files ('critical-files'). |
| `critical-file-count` | `number` | `10` | Minimum number of modified files to trigger the minimum approver requirement ('approvals-for-critical-changes'). |
| `critical-files` | `string` | `''` | Newline-separated list of files that, if modified, trigger the minimum approver requirement ('approvals-for-critical-changes'). |# Validate External Contributor Approvals

## Validate external contributor approvals: `validate-external-contributor-approvals.yaml`

This reusable workflow requires a minimum number of approvals for pull requests from external contributors (users who are not members, owners, or collaborators in the repository).

> [!NOTE]
> It is best practice to also configure repository settings to require approval for CI runs from external contributors (under https://github.com/ORG/REPO/settings/actions: "Approval for running fork pull request workflows from contributors").

### Usage
Add the following file to each repository where this check should be performed.
```yaml
# .github/workflows/validate-approvals.yml
name: Validate PR Approvals

on:
  pull_request_review:
    types: [submitted]

jobs:
  validate-external-approvals:
    uses: NOAA-EMC/ci-validate-pr/validate-external-contributor-approvals.yaml@a3c7e3ee61dc1c6f879a59d82d1f48e1e9f9fffa
    with:
      approvals-for-external-contributors: 2
```

### Inputs

| Input | Type | Default | Description |
|---|---|---|---|
| `approvals-for-external-contributors` | `number` | `0` | Required number of approvers for PRs from external contributors. |

---
_Generative AI tools are used to assist with developing this code._
_The code has been reviewed, edited, and validated by NWS staff._
