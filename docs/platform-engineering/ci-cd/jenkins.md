# Jenkins

Jenkins is an extensible automation server commonly used for continuous integration and delivery. Its flexibility comes from Pipeline, distributed agents, credentials, and plugins; those same capabilities require deliberate security, lifecycle management, and operational ownership.

## Controller, Agents, and Executors

The controller stores configuration, schedules work, serves the UI and API, and coordinates Pipeline state. Agents provide executors and workspaces where build steps run.

```text
source-control event
        |
 Jenkins controller -> schedules Pipeline
        |
        v
 ephemeral or persistent agent -> build, test, package, publish
```

Do not run ordinary builds on the controller. Isolate it from untrusted build workloads and use agents with the least network and credential access required. Ephemeral agents improve cleanliness and scaling but still need controlled images, caches, logs, and startup capacity.

## Pipeline as Code

A `Jenkinsfile` keeps delivery behaviour beside application code and subjects it to review:

```groovy
pipeline {
    agent none
    stages {
        stage('Build and test') {
            agent { label 'linux-jdk' }
            steps {
                sh './gradlew clean check'
            }
        }
    }
    post {
        always {
            junit 'build/test-results/**/*.xml'
        }
    }
}
```

Declarative Pipeline provides a structured model with directives for agents, stages, environment, options, and post conditions. Scripted Pipeline offers more direct Groovy control. Prefer the simplest declarative structure that expresses the workflow.

## Stages and Delivery Flow

A pipeline should make feedback and promotion visible:

```text
checkout -> compile -> fast tests -> package -> scan
         -> integration tests -> publish immutable artifact
         -> deploy -> verify -> promote or roll back
```

Build once and promote the same immutable artifact. Separate artifact creation from environment deployment. Use timeouts, concurrency controls, approvals, and rollback according to risk. A green deployment command is not proof that users can use the release; add health and outcome verification.

## Multibranch Pipelines

Multibranch projects discover branches and pull requests that contain a `Jenkinsfile`. Treat pull-request code as untrusted: a pipeline change can read workspace data, run processes, access networks, and attempt to expose credentials available to the build.

Do not provide production secrets or privileged agents to untrusted forks. Separate trusted post-merge or deployment workflows from pre-merge validation where necessary.

## Credentials

Store credentials through Jenkins credential facilities or an integrated external secret manager and reference them by identifier. Scope each credential to the narrowest folder, item, environment, and stage that requires it.

```groovy
withCredentials([string(credentialsId: 'registry-token', variable: 'REGISTRY_TOKEN')]) {
    sh 'publish-artifact --token-env REGISTRY_TOKEN'
}
```

Masking reduces accidental log exposure but is not a complete security boundary. A build that can use a secret may be able to exfiltrate it. Avoid shell tracing, command-line exposure, archived workspaces, and tools that print their environment.

## Shared Libraries

Shared Libraries centralise stable delivery capabilities across repositories. Keep APIs small, version changes, test them, and allow product repositories to retain visible control of their meaningful stages.

Trusted libraries can execute powerful APIs outside normal sandbox constraints. Anyone who can change a trusted library may effectively control Jenkins, its agents, and accessible credentials. Restrict repository permissions and review trusted changes accordingly.

Avoid an enormous delivery framework that makes every repository depend on hidden behaviour. Standardise security and proven mechanics while allowing local configuration where product differences are real.

## Plugins and Upgrades

Plugins extend source control, credentials, agents, reports, UI, and pipeline steps. Every plugin adds compatibility, security, upgrade, and abandonment risk.

Maintain:

- an approved, minimal plugin set;
- a supported Jenkins release and Java runtime;
- tested controller and plugin upgrades;
- configuration as code where practical;
- backups of required controller state and separately protected secrets;
- a restoration test, not merely a backup job.

Test changes in a representative non-production controller before rollout and read security advisories.

## Workspaces, Caches, and Artifacts

Workspaces are temporary working areas, not durable artifact stores. Avoid sharing one workspace between concurrent builds. Clean or replace agents according to the threat and contamination model.

Caches can improve speed but require keys, size limits, validation, and poisoning controls. Publish versioned artifacts to a repository designed for retention and promotion. Apply retention policies to builds, logs, test reports, artifacts, and fingerprints.

## Testing and Diagnostics

Validate Jenkinsfiles and shared-library code before broad rollout. Test reusable pipeline steps with controlled inputs, and run representative consumer pipelines for integration confidence.

For a failed build retain:

- stage and step context;
- console logs with timestamps and secret protection;
- test and static-analysis reports;
- relevant artifacts and checksums;
- agent image, tool versions, commit, parameters, and environment identifiers;
- controller and agent logs for infrastructure failures.

Separate product failure, test failure, pipeline defect, agent capacity, dependency outage, and controller problem. Automatic retries are appropriate only for failures classified as transient and safe to repeat.

## Security and Operations

Authenticate users through an organisational identity provider and grant least privilege. Protect the controller, restrict administrative APIs, isolate agent networks, use CSRF and browser protections, and audit configuration and credential access.

Monitor queue time, executor utilisation, agent provisioning, build duration, controller resource use, disk growth, failed jobs, and plugin health. Capacity problems are often visible first as queue delays or widespread timeouts.

## Readiness Checklist

You should be able to:

- explain controller, agent, executor, node, workspace, stage, and step;
- write a reviewed declarative pipeline with clear artifact flow;
- isolate untrusted change builds from privileged credentials and agents;
- scope credentials and explain the limits of log masking;
- design and govern a small versioned shared library;
- manage plugins, upgrades, backups, and restoration;
- diagnose queue, agent, pipeline, test, and product failures separately.

## Official References

- [Jenkins user handbook](https://www.jenkins.io/doc/book/)
- [Pipeline](https://www.jenkins.io/doc/book/pipeline/)
- [Pipeline syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)
- [Securing Jenkins](https://www.jenkins.io/doc/book/security/)
- [Shared Libraries](https://www.jenkins.io/doc/book/pipeline/shared-libraries/)

Return to [Continuous Integration and Delivery](./README.md).
