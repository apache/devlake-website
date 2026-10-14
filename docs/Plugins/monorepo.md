---
title: "Monorepo"
description: >
  Monorepo Plugin
---

## Summary

DevLake's scope model is one scope per repository, so a repository that hosts several
logically separate projects collapses into a single set of DORA numbers. The `monorepo`
plugin splits such a repository into **sub-projects** and computes per-sub-project
delivery metrics.

Like [DORA](../DORA.md) and [Linker](./linker.md), it is a metric plugin: it works on the
domain layer after data collection, and runs after `dora`.

It attributes:

- **Deployments** to a sub-project by matching the name of the CI job that performed the
  deployment against a regular expression.
- **Pull requests** to a sub-project by matching the PR's labels.

From those two attributions it produces per-sub-project
[Deployment Frequency](../Metrics/DeploymentFrequency.md) and
[Lead Time for Changes](../Metrics/LeadTimeForChanges.md).

[Change Failure Rate](../Metrics/CFR.md) and
[Median Time to Restore Service](../Metrics/MTTR.md) are **not** computed per sub-project,
because they require incident data and incidents are not attributed to sub-projects.

## Supported Versions

The monorepo plugin was added after the `v1.0.3-beta18` release and is available in all
later releases.

## Configuration

The plugin is configured per project, on the project's settings page.

1. Open **Projects** → your project → **Settings**.
2. Tick **Enable Monorepo Sub-Projects**.
3. Add one row per sub-project and fill in the three fields below.
4. Click **Save**, then re-run the project's blueprint.

| Field                     | Description                                                                                                                             | Example              |
|:--------------------------|:----------------------------------------------------------------------------------------------------------------------------------------|:---------------------|
| **Sub-project name**      | Identifies the sub-project in the output tables and dashboards. Must be unique within the project.                                      | `serviceA`           |
| **Labels**                | Comma-separated pull request labels that mark a PR as belonging to this sub-project. Matching is **exact and case-sensitive**.           | `serviceA,svc-a`     |
| **Deploy job pattern**    | A [Go regular expression](https://pkg.go.dev/regexp/syntax) matched against `cicd_tasks.name` to recognise this sub-project's deploy jobs. | `^deploy-serviceA$`  |

`unattributed` and `All` are reserved and cannot be used as sub-project names.

The same options can be set through the API or the Config UI's
[advanced mode](../Configuration/AdvancedMode.md) as the project's `monorepo` metric
plugin option:

```json
{
  "pluginName": "monorepo",
  "enable": true,
  "pluginOption": {
    "subProjects": [
      {
        "name": "serviceA",
        "prLabels": ["serviceA", "svc-a"],
        "deployJobPattern": "^deploy-serviceA$"
      },
      {
        "name": "serviceB",
        "prLabels": ["serviceB"],
        "deployJobPattern": "^deploy-serviceB$"
      }
    ],
    "includeUnattributed": true
  }
}
```

`includeUnattributed` (default `true`) controls what happens to pull requests and
deployments that belong to the project but match none of the configured sub-projects:
with `true` they are recorded under the sentinel sub-project `unattributed`, so you can
see how much of the repository is not yet classified; with `false` they are left
unclassified.

## How attribution works

### Deployments

Every deployment of the project is joined to the CI/CD tasks of the pipeline that produced
it, and each task of type `Deployment` has its name matched against each sub-project's
`deployJobPattern`.

- A pipeline that runs the deploy jobs of several sub-projects produces **one row per
  sub-project**, so dashboards can `GROUP BY sub_project` without double counting.
- Deployments imported directly from a provider's deployment API (rather than generated
  from a pipeline) carry no job name and cannot be attributed.

### Pull requests

All pull requests of the project are attributed — open, closed and merged — by label.
A pull request belongs to exactly one sub-project: when it carries the labels of more than
one, the **first matching sub-project in the configured order wins**, so order the list
from most to least specific.

PR coding, pickup, review, deploy and cycle time are not recomputed by this plugin; they
are taken from DORA's own `project_pr_metrics` rows and tagged with the sub-project.

### Mismatch warnings

After tagging, the plugin cross-checks each PR's label-derived sub-project against the
sub-project of the deployment that DORA says actually shipped it. Disagreements are logged
as warnings and never fail the pipeline — labels always win. They usually point at a
`prLabels` or `deployJobPattern` entry that needs fixing, though a pipeline deploying
several sub-projects at once will produce such warnings legitimately.

## Output

| Table                                                                                     | Description                                                                                |
|:------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------|
| [`cicd_deployment_subprojects`](../DataModels/DevLakeDomainLayerSchema.md#cicd_deployment_subprojects) | Maps each deployment to the sub-project(s) it deployed.                                     |
| [`pull_requests.sub_project`](../DataModels/DevLakeDomainLayerSchema.md#pull_requests)     | The sub-project a pull request belongs to.                                                  |
| [`pull_request_commits.sub_project`](../DataModels/DevLakeDomainLayerSchema.md#pull_request_commits) | Mirrors the owning pull request's sub-project.                                              |
| [`project_pr_metrics.sub_project`](../DataModels/DevLakeDomainLayerSchema.md#project_pr_metrics) | Tags DORA's per-PR metrics with the sub-project.                                             |

A `NULL` `sub_project` means the row was never classified — single-repo projects, or rows
the plugin has not processed. Dashboards render these as the single implicit group `All`
via `COALESCE(sub_project, 'All')`.

Attribution is rebuilt from scratch on every run, so changing the configuration and
re-running the blueprint reclassifies existing data.

:::note
The plugin also populates `monorepo_subproject_deployments` and
`monorepo_subproject_pr_metrics`. Both are deprecated and will be removed in a future
release — query the tables above instead.
:::

## Dashboard

A built-in Grafana dashboard, **Monorepo Sub-Projects**, visualises the output:

- Deployment Count by Sub-Project
- Deployment Frequency over Time
- Change Lead Time Breakdown by Sub-Project
- Unattributed PRs
- Awaiting Deployment
- Attributed Deployments
