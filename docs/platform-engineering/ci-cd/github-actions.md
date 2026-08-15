# GitHub Actions

GitHub Actions runs repository automation described by YAML workflows. Events trigger workflows, jobs run on selected runners, and steps invoke actions or shell commands.

## Execution Model

```text
event -> workflow -> jobs -> steps -> commands/actions -> artifacts and status
```

Jobs are isolated unless they exchange declared artifacts, cache entries, or outputs. Express dependencies with `needs`, use concurrency controls for superseded deployments, and keep pull-request validation separate from privileged deployment.

## Security

- Pin third-party actions to trusted versions and review their permissions.
- Set the workflow or job `permissions` block to the minimum required.
- Treat pull-request source, issue text, branch names, and generated outputs as untrusted input.
- Prefer short-lived cloud federation to long-lived service-account keys.
- Protect deployment environments with reviewers or branch rules where risk requires it.
- Do not print secrets or pass them through artifacts and untrusted commands.

Repository secrets are not general configuration storage. Use non-secret variables for public identifiers and environment-specific values that do not require confidentiality.

## Builds and Deployments

Install from lockfiles, run checks before packaging, and upload or promote an immutable artifact rather than rebuilding differently during deployment. Give runtime and deployment identities separate permissions.

Caches improve speed but are not authoritative build inputs. Artifacts are explicit outputs with retention and access implications. Record test results, image identifiers, service URLs, and useful summaries without exposing credentials.

## Project Connections

The repositories use GitHub Actions to test and deploy Flask and React services to Cloud Run, build and sign Android artifacts, and validate health-data CSV storage with PowerShell.

## Related Guides

- [Continuous Integration and Delivery](./README.md)
- [Docker](../docker.md)
- [Cloud Run](../cloud/gcp/cloud-run.md)
- [IAM](../cloud/gcp/iam.md)

Return to [Continuous Integration and Delivery](./README.md).
