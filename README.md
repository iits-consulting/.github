# iits-consulting GitHub Workflow Templates

Organization-wide [reusable workflow templates](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows) for the `iits-consulting` GitHub organization.

These templates appear in the **Actions** tab when creating a new workflow in any repository within the organization.

## Workflow Templates

| Template | Description |
|----------|-------------|
| **docker-on-merge-to-main** | Build and push a stable Docker image on merge to the default branch, with semver tagging based on PR labels |
| **docker-any-branch** | Build and push a pre-release Docker image on feature branch pushes |
| **helm-on-merge-to-main** | Lint, version-bump, and push a stable Helm chart to ACR on merge to the default branch |
| **helm-any-branch-push** | Lint, validate, and push a pre-release Helm chart to ACR on feature branch pushes |
| **enforce-pr-labels** | Require `major`, `minor`, or `patch` labels on pull requests |

## Organization Workflows

| Workflow | Description |
|----------|-------------|
| **enforce-branch-protection** | Automatically enforce branch protection rules on newly created repositories (runs hourly) |

## Directory Structure

```
workflow-templates/           # Starter workflow YAML files
  properties/                 # Metadata JSON for each template (shown in GitHub UI)
  docker-icon-color.svg       # Icon for Docker templates
  helm-icon-color.svg         # Icon for Helm templates
.github/workflows/            # Org-level automation workflows
```

## Compliance: Code Change Review Enforcement

This repository serves as the **central enforcement mechanism** for code change reviews across the `iits-consulting` GitHub organization, satisfying the following ISO 27001:2017 controls:

| Control | Name | How It Is Satisfied |
|---------|------|---------------------|
| **A.14.2.2** | System change control procedures | Branch protection requires PR approval before merge; the `enforce-branch-protection` workflow ensures this is applied org-wide |
| **A.12.1.2** | Change management | All code changes must pass through a pull request with at least one independent reviewer |

### How It Works

The [`enforce-branch-protection`](.github/workflows/enforce-branch-protection.yml) workflow runs **every hour** and automatically applies the following rules to the default branch of every repository in the organization:

1. **Required pull request reviews** — At least **1 approving review** is required before merging
2. **Dismiss stale reviews** — If new commits are pushed after approval, the approval is invalidated and a new review is required
3. **Admin enforcement** — Administrators cannot bypass branch protection rules, ensuring no exceptions
4. **Automatic remediation** — Newly created repositories are detected and configured automatically, eliminating manual setup errors

### Evidence for Auditors

| Evidence Type | Location |
|---------------|----------|
| Automated enforcement workflow | [`.github/workflows/enforce-branch-protection.yml`](.github/workflows/enforce-branch-protection.yml) |
| Workflow run history (hourly) | [Actions tab — Enforce Branch Protection](../../actions/workflows/enforce-branch-protection.yml) |
| Branch protection API state per repo | `gh api repos/iits-consulting/{repo}/branches/main/protection` |
| PR review history per repo | Visible in each repository's pull request history |
| Vanta automated test | `code-review-application-config` — "Application changes reviewed" |

### Workflow Templates as Additional Controls

The workflow templates in this repository provide further change management controls:

- **enforce-pr-labels** — Requires semantic version labels (`major`, `minor`, `patch`) on every PR, enforcing conscious classification of change impact
- **docker/helm-on-merge-to-main** — Stable releases are only built from the default branch (i.e., after PR approval and merge), preventing unapproved code from being deployed
- **docker/helm-any-branch** — Feature branch builds produce pre-release artifacts only, clearly separated from production versions

## Security Practices

All workflow templates follow [GitHub's security hardening guidelines](https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions):

- **SHA-pinned actions** — Every third-party action is pinned to a full commit SHA to prevent supply chain attacks
- **Least-privilege permissions** — Explicit `permissions:` blocks grant only the minimum `GITHUB_TOKEN` scopes needed
- **No script injection** — Step outputs are passed via `env:` mappings, never interpolated directly in `run:` blocks
- **Verified tool installs** — Binary downloads (e.g., kubeconform) are version-pinned with SHA-256 checksum verification
- **Secret hygiene** — Secrets are passed through environment variables, never expanded inline in shell commands

## Required Secrets

### Docker templates
- `AZURE_CREDENTIALS` — Azure service principal credentials
- `AZURE_DOCKER_USERNAME` — ACR username
- `AZURE_DOCKER_PASSWORD` — ACR password

### Helm templates
- `ACR_RELEASER_TOKEN` — ACR token for Helm registry push

### Branch protection workflow
- `ORG_ADMIN_TOKEN` — GitHub PAT with org admin permissions

## License

[MIT](LICENSE)