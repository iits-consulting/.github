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
