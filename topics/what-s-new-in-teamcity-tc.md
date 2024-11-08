[//]: # (title: What's New in TeamCity On-Premises 2024.11)

<snippet id="2024-11-tc">


## Pull Request Filters
{instance="tc"}

Starting with version 2024.11, TeamCity [branch filters](branch-filter.md#Pull+Request+Branch+Filters) support the `+|-pr:` syntax. This syntax allows you to create fine-grained filter expressions that track pull requests by a number of parameters: author, user role, target and source branches, and more.

The new syntax is currently available only for triggers. When setting up trigger settings, click a magic wand next to the **Branch Filter** field to invoke an expression editor.

<img src="dk-visual-pr-filters-editor.png" alt="Filter Expression Editor" width="706"/>

Learn more: [](branch-filter.md#Pull+Request+Branch+Filters)


## New Dependency Cache Features
{instance="tc"}

This TeamCity version introduces three new [build features](adding-build-features.md) designed to cache dependencies downloaded during a build, speeding up subsequent builds and enabling offline completion:

* **Maven Cache** — caches dependencies used by [](maven.md) build steps
* **Gradle Cache** — caches dependencies used by [](gradle.md) build steps
* **NuGet Cache** — caches NuGet packages used by [](net.md) build steps

All three features require no configuration: add/enable a required build feature to enable caching, and remove/disable it to force agents to re-download missing dependencies with each build.

Learn more: [](dependency-caches.md)


## Perforce Integration Enhancements
{instance="tc"}

* When a [Perforce VCS Root](perforce.md) is configured to check out sources by label (the **Label/changelist to sync** setting), TeamCity now records the revision number in a new `vcsRoot.{externalId}.changelist` parameter. This quality-of-life improvement enables clear identification of the synced revision.


## Offload Your Builds to Kubernetes
{instance="tc"}

In a traditional TeamCity paradigm, you should set up, maintain, and manage build agents that process builds. This applies to both cloud-hosted and local agents.

Starting with version 2024.11, you will be able to take an alternative route: offload building tasks to the K8s cluster. Set up the new integration and start building right away: our K8s executor will generate pod definitions based on build parameters and required containers and submit them to K8s cluster. As a result, your cluster will act as an orchestrator that manages builds and "agents" lifecycle.

<img src="dk-k8s-integration-overview.png" alt="K8S integration" width="706"/>

Learn more: [](external-kubernetes-agents.md), [Kubernetes connection](configuring-connections.md#Kubernetes)


## Approve Multiple Builds at Once
{instance="tc"}

TeamCity enables you to require explicit permission from designated users before starting certain builds. Approval is needed in two cases:

* When a build configuration has the [](build-approval.md) feature to prevent accidental runs.
* When [](untrusted-builds.md) are enabled, ensuring unverified pull request changes are reviewed before builds begin.

Previously, both options required approving each build individually within a chain. Starting with version 2024.11, clicking **Approve** now grants permission for all builds in the chain at once.

<img src="dk-approve-chain.png" width="706" alt="Approve chain"/>

Learn more: [](build-approval.md), [](untrusted-builds.md)

## Upload Custom Kotlin Libraries
{instance="tc"}

Starting with this version, you can upload custom Kotlin libraries as `.jar` to you TeamCity server.

<img src="custom-dsl-library-upload.png" width="706" alt="Upload custom Kotlin library"/>

To start using these libraries in your project's [.kts files](kotlin-dsl.md), add Maven dependencies to required `pom.xml`s.

Learn more: [](kotlin-dsl.md#Add+Custom+Kotlin+Libraries)


## Partial Chain Execution
{instance="tc"}

Version 2024.11 introduces two configuration parameters that accept tags and IDs of upstream builds. Doing so allows you to run a portion of your build chain.

<img src="dk-subchains-gif.gif" width="706" alt="Subchains"/>

These parameters can be a permanent part of your configuration or occasionally added via the [Run Custom Build](running-custom-build.md) dialog when you need to run a specific sub-chain.

Learn more: [](build-chain.md#Partial+Chain+Execution)


## Execute Meta-Runners Inside Containers
{instance="tc"}

Along with setting [container options](container-wrapper.md) for individual build steps within a [meta-runner](working-with-meta-runner.md), you can now define shared container settings at the meta-runner level.

<img src="dk-docker-container-settings.png" width="706" alt="Container settings in steps and meta-runners"/>

Individual step settings still take precedence, enabling each step to run in its own container if specified.

Learn more: [](working-with-meta-runner.md#Launch+Meta-Runners+in+Containers)


## AWS Integration Enhancements
{instance="tc"}

[Amazon EC2 cloud profiles](setting-up-teamcity-for-amazon-ec2.md) will no longer use access keys or the default credentials provider chain, shifting to authentication through [TeamCity AWS connections](configuring-connections.md#AmazonWebServices). This change consolidates all authentication settings into a single connection that can be shared across multiple features (cloud profiles, [S3 artifact storages](storing-build-artifacts-in-amazon-s3.md), [](aws-credentials.md) build features, and so on).

Existing connections will retain legacy authentication but recommend migrating to connection-based access. New EC2 cloud profiles will support only the new authentication method.


## Miscellaneous Changes
{instance="tc"}

* The [](artifacts-migration-tool.md) now supports migration to and from Microsoft Azure storages. This tool allows you to easily transfer build artifacts from one storage to another. Note that you need to install an unbundled plugin to set up Azure storages: [Azure Artifact Storage](https://plugins.jetbrains.com/plugin/9617-azure-artifact-storage).
* All messages written to `teamcity-server.log` during a server startup are now duplicated to the [teamcity-startup.log](teamcity-server-logs.md). This log ensures major boot events are logged to a separate file, which may assist in troubleshooting server startup issues.
* The [HashiCorp Vault connection](hashicorp-vault.md) no longer provides a setting that allows builds to proceed when TeamCity is unable to retrieve Vault secrets. Starting with version 2024.11, such build will fail.
* TeamCity [metrics](teamcity-monitoring-and-diagnostics.md#Metrics) set now includes new experimental metrics:
    * the `log_messages` metric allows you to obtain the total number of [Log4j](teamcity-server-logs.md) messages. This metric reports a separate number of log messages for each category (`ACTIVITIES`, `AGENT`, `STARTUP`, and others) and severity (`INFO` or `WARN`).
    * the `persistTasks_global_settings_count` and `persistTasks_project_configs_count` metrics report same values as the **Settings Persist Status** tab of the [Diagnostics](teamcity-monitoring-and-diagnostics.md) page. Both metrics are reported only for the main [node](multinode-setup.md).

</snippet>

