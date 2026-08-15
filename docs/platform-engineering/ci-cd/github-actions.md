# GitHub Actions

GitHub Actions runs repository automation described by YAML workflows. Events trigger workflows, jobs run on selected runners, and steps invoke actions or shell commands.

## Execution Model

```text
event -> workflow -> jobs -> steps -> commands/actions -> artifacts and status
```

Jobs are isolated unless they exchange declared artifacts, cache entries, or outputs. Express dependencies with `needs`, use concurrency controls for superseded deployments, and keep pull-request validation separate from privileged deployment.

## Worked CI Workflow

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read

concurrency:
  group: ci-${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 15

    steps:
      - name: Check out source
        uses: actions/checkout@v4

      - name: Configure Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - name: Install locked dependencies
        run: npm ci

      - name: Run repository checks
        run: npm run check

      - name: Build production assets
        run: npm run build
```

The workflow grants read-only repository contents, cancels superseded work on the same ref, bounds execution time, installs from the lockfile, and verifies a production build. Select action versions through review; higher-assurance workflows can pin the reviewed commit digest and update it deliberately.

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

## Deployment Shape

```text
pull request: lint + test + build
                    |
                    v
main:        build immutable artifact
                    |
             protected environment
                    |
                    v
             deploy same artifact
                    |
             smoke check + rollback signal
```

Use a separate deployment job with `needs` so quality gates are explicit. Attach only the cloud identity and environment secrets required by that job. Prefer workload identity federation so the job receives a short-lived credential rather than storing a long-lived cloud key.

## Common Failure Modes

- granting write permissions to the whole workflow by default;
- exposing deployment credentials to untrusted pull-request code;
- using mutable third-party actions without a review/update policy;
- rebuilding a different artifact in each environment;
- interpolating untrusted branch or issue text into a shell command;
- using a dependency cache as though it were a release artifact;
- allowing an unbounded hung test or deployment;
- hiding flaky failures behind automatic retries without reporting them.

## Project Connections

The repositories use GitHub Actions to test and deploy Flask and React services to Cloud Run, build and sign Android artifacts, and validate health-data CSV storage with PowerShell.

## Official References

- [Workflow syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)
- [Expressions](https://docs.github.com/en/actions/reference/workflows-and-actions/expressions)
- [Security hardening for GitHub Actions](https://docs.github.com/en/actions/how-tos/security-for-github-actions/security-guides/security-hardening-for-github-actions)

## Related Guides

- [Continuous Integration and Delivery](./README.md)
- [Docker](../docker.md)
- [Cloud Run](../cloud/gcp/cloud-run.md)
- [IAM](../cloud/gcp/iam.md)

Return to [Continuous Integration and Delivery](./README.md).
