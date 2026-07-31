---
title: "Supported Data Sources"
description: >
  Data sources that DevLake supports
sidebar_position: 5
---

## Data Sources and Data Plugins

Apache DevLake(incubating) supports the following data sources. The data from each data source is collected with one or
more plugins. Detailed plugin docs can be found [here](/docs/Plugins).

| Data Source         | Domain(s)                                                                   | Supported Versions                 | Config UI Availability  | Triggered Plugins             | Collection Mode                                                     |
|---------------------|-----------------------------------------------------------------------------|------------------------------------|-------------------------|-------------------------------|---------------------------------------------------------------------|
| GitHub              | Source Code Management, Code Review, Issue Tracking, CI/CD (GitHub Actions) | Cloud                              | Available               | `github`, `gitextractor`      | Incremental Sync                                                    |
| GitLab              | Source Code Management, Code Review, Issue Tracking, CI/CD (GitLab CI)      | Cloud, Community Edition 11+       | Available               | `gitlab`, `gitextractor`      | Full Refresh, Incremental Sync(for `issues`,`MRs`)                  |
| Jira                | Issue Tracking                                                              | Cloud, Server/Data Center 7.x and above  | Available               | `jira`                        | Full Refresh, Incremental Sync(for `issues` and related)            |
| Jenkins             | CI/CD                                                                       | 2.263.x+                           | Available               | `jenkins`                     | Incremental Sync                                                    |
| BitBucket (Beta)    | Source Code Management, Code Review                                         | Cloud                              | Advanced Mode Available | `bitbucket`, `gitextractor`   | Full Refresh                                                        |
| TAPD (Beta)         | Issue Tracking                                                              | Cloud                              | Advanced Mode Available | `tapd`                        | Full Refresh, Incremental Sync(for `stories`, `bugs`, `tasks`)      |
| Teambition (Beta)   | Issue Tracking                                                              | Cloud                              | Advanced Mode Available | `teambition`                  | Full Refresh                                                        |
| Zentao (Beta)       | Issue Tracking                                                              | v17.x, v18.x                       | Advanced Mode Available | `zentao`                      | Full Refresh                                                        |
| Gitee (WIP)         | Source Code Management, Code Review, Issue Tracking                         | Cloud                              | Not Available           | `gitee`, `gitextractor`       | Full Refresh, Incremental Sync(for `issues`,`MRs`)                  |
| PagerDuty           | Issue Tracking                                                              | Cloud                              | Available               | `pagerduty`                   | Incremental Sync                                                    |
| Opsgenie            | Issue Tracking                                                              | Cloud                              | Available               | `opsgenie`                    | Full Refresh (for `users`,`teams`), Incremental Sync (for `issues`) |
| incident.io         | Issue Tracking                                                              | Cloud                              | Available               | `incidentio`                  | Full Refresh                                                        |
| Feishu (WIP)        | Calendar                                                                    | Cloud                              | Not Available           | `feishu`                      | Full Refresh                                                        |
| AE                  | Source Code Management                                                      | On-prem                            | Not Available           | `ae`                          | Full Refresh                                                        |
| Sonarqube           | CODE QUALITY                                                                | SonarQube v8.x, v9.x               | Available               | `sonarqube`                   | Full Refresh                                                        |
| Bamboo CI(WIP)      | CI/CD                                                                       | v6.8.1, Server                     | Not Available           | `bamboo`                      | Full Refresh                                                        |
| Azure Devops (Beta) | CI/CD, Source Code Management, Code Review                                  | Cloud                              | Available               | `azuredevops`, `gitextractor` | Full Refresh                                                        |
| CircleCI            | CI/CD                                                                       | Cloud                              | Available               | `circleci`                    | Full Refresh                                                        |


## Data Collection Scope By Each Plugin

This table shows the entities collected by each plugin. Domain layer entities in this table are consistent with the
entities [here](/DataModels/DevLakeDomainLayerSchema.md).
✅ : Collect by default.
💪 : Collect not by default. You need to add the corresponding subtasks to collect these entities in
the [advanced mode](../Configuration/AdvancedMode.md).

| Domain Layer Entities                                                                       | ae | dora | gitextractor | incoming webhook | github | gitlab | jenkins | jira | refdiff | tapd | sonarqube | bamboo | azuredevops | opsgenie | circleci |
|---------------------------------------------------------------------------------------------|----|------|--------------|------------------|--------|--------|---------|------|---------|------|-----------|--------|-------------|----------|----------|
| [accounts](../DataModels/DevLakeDomainLayerSchema.md/#accounts)                             |    |      |              |                  | ✅      | ✅      |         | ✅    |         | ✅    | ✅         |        |             |          | ✅        |
| [board_issues](../DataModels/DevLakeDomainLayerSchema.md/#board_issues)                     |    |      |              |                  | ✅      | ✅      |         | ✅    |         | ✅    |           |        |             | ✅        |          |
| [board_repos](../DataModels/DevLakeDomainLayerSchema.md/#board_repos)                       |    |      |              |                  | ✅      | ✅      |         |      |         |      |           |        |             |          |          |
| [board_sprints](../DataModels/DevLakeDomainLayerSchema.md/#board_sprints)                   |    |      |              |                  | ✅      |        |         | ✅    |         | ✅    |           |        |             |          |          |
| [boards](../DataModels/DevLakeDomainLayerSchema.md/#boards)                                 |    |      |              |                  | ✅      | ✅      |         | ✅    |         | ✅    |           |        |             | ✅        |          |
| [cicd_pipeline_commits](../DataModels/DevLakeDomainLayerSchema.md/#cicd_pipeline_commits)   |    | ✅    |              |                  | ✅      | ✅      | ✅       |      |         |      |           | ✅      | ✅           |          | ✅        |
| [cicd_pipelines](../DataModels/DevLakeDomainLayerSchema.md/#cicd_pipelines)                 |    | ✅    |              |                  | ✅      | ✅      | ✅       |      |         |      |           | ✅      | ✅           |          | ✅        |
| [cicd_scopes](../DataModels/DevLakeDomainLayerSchema.md/#cicd_scopes)                       |    | ✅    |              |                  | ✅      | ✅      | ✅       |      |         |      |           | ✅      | ✅           |          | ✅        |
| [cicd_tasks](../DataModels/DevLakeDomainLayerSchema.md/#cicd_tasks)                         |    | ✅    |              | 💪               | ✅      | ✅      | ✅       |      |         |      |           | ✅      | ✅           |          | ✅        |
| [commit_file_components](../DataModels/DevLakeDomainLayerSchema.md/#commit_file_components) |    |      | ✅            |                  |        |        |         |      |         |      |           |        |             |          |          |
| [commit_files](../DataModels/DevLakeDomainLayerSchema.md/#commit_files)                     |    |      | ✅            |                  |        |        |         |      |         |      |           |        |             |          |          |
| [commit_line_change](../DataModels/DevLakeDomainLayerSchema.md/#commit_line_change)         |    |      | ✅            |                  |        |        |         |      |         |      |           |        |             |          |          |
| [commit_parents](../DataModels/DevLakeDomainLayerSchema.md/#commit_parents)                 |    |      | ✅            |                  |        |        |         |      |         |      |           |        |             |          |          |
| [commits](../DataModels/DevLakeDomainLayerSchema.md/#commits)                               | ✅  |      | ✅            |                  | 💪     | 💪     |         |      |         |      |           |        |             |          |          |
| [commits_diffs](../DataModels/DevLakeDomainLayerSchema.md/#commits_diffs)                   |    |      |              |                  |        |        |         |      | ✅       |      |           |        |             |          |          |
| [components](../DataModels/DevLakeDomainLayerSchema.md/#components)                         |    |      |              |                  |        |        |         |      |         |      |           |        |             |          |          |
| [finished_commits_diffs](../DataModels/DevLakeDomainLayerSchema.md/#finished_commits_diffs) |    |      |              |                  |        |        |         |      |         |      |           |        |             |          |          |
| [issue_changelogs](../DataModels/DevLakeDomainLayerSchema.md/#issue_changelogs)             |    |      |              |                  |        |        |         | ✅    |         | ✅    |           |        |             |          |          |
| [issue_comments](../DataModels/DevLakeDomainLayerSchema.md/#issue_comments)                 |    |      |              |                  | ✅      |        |         |      |         | ✅    |           |        |             |          |          |
| [issue_commits](../DataModels/DevLakeDomainLayerSchema.md/#issue_commits)                   |    |      |              |                  |        |        |         | ✅    |         | ✅    |           |        |             |          |          |
| [issue_labels](../DataModels/DevLakeDomainLayerSchema.md/#issue_labels)                     |    |      |              |                  | ✅      | ✅      |         |      |         | ✅    |           |        |             |          |          |
| [issue_repo_commits](../DataModels/DevLakeDomainLayerSchema.md/#issue_repo_commits)         |    |      |              |                  |        |        |         | ✅    |         |      |           |        |             |          |          |
| [issue_worklogs](../DataModels/DevLakeDomainLayerSchema.md/#issue_worklogs)                 |    |      |              |                  |        |        |         | ✅    |         | ✅    |           |        |             |          |          |
| [issues](../DataModels/DevLakeDomainLayerSchema.md/#issues)                                 |    |      |              |                  | ✅      |        |         | ✅    |         | ✅    |           |        |             | ✅        |          |
| [project_issue_metrics](../DataModels/DevLakeDomainLayerSchema.md/#project_issue_metrics)   |    | ✅    |              |                  | ✅      | ✅      |         | ✅    |         | ✅    |           |        |             |          |          |
| [project_mapping](../DataModels/DevLakeDomainLayerSchema.md/#project_mapping)               |    | ✅    |              |                  | ✅      | ✅      | ✅       | ✅    |         | ✅    |           |        |             |          |          |
| [project_metrics](../DataModels/DevLakeDomainLayerSchema.md/#project_metrics)               |    | ✅    |              |                  | ✅      | ✅      | ✅       | ✅    |         | ✅    |           |        |             |          |          |
| [project_pr_metrics](../DataModels/DevLakeDomainLayerSchema.md/#project_pr_metrics)         |    | ✅    |              |                  | ✅      | ✅      |         |      |         | ✅    |           |        |             |          |          |
| [project](../DataModels/DevLakeDomainLayerSchema.md/#project)                               |    | ✅    |              |                  | ✅      | ✅      | ✅       | ✅    |         | ✅    |           |        |             |          | ✅        |
| [pull_request_comments](../DataModels/DevLakeDomainLayerSchema.md/#pull_request_comments)   |    |      |              |                  | ✅      | ✅      |         |      |         |      |           |        |             |          |          |
| [pull_request_commits](../DataModels/DevLakeDomainLayerSchema.md/#pull_request_commits)     |    |      |              |                  | ✅      | ✅      |         |      |         |      |           |        | ✅           |          |          |
| [pull_request_issues](../DataModels/DevLakeDomainLayerSchema.md/#pull_request_issues)       |    |      |              |                  | ✅      |        |         |      |         |      |           |        |             |          |          |
| [pull_request_labels](../DataModels/DevLakeDomainLayerSchema.md/#pull_request_labels)       |    |      |              |                  | ✅      | ✅      |         |      |         |      |           |        |             |          |          |
| [pull_requests](../DataModels/DevLakeDomainLayerSchema.md/#pull_requests)                   |    |      |              |                  | ✅      | ✅      |         |      |         |      |           |        | ✅           |          |          |
| [ref_commits](../DataModels/DevLakeDomainLayerSchema.md/#ref_commits)                       |    |      |              |                  |        |        |         |      | ✅       |      |           |        |             |          |          |
| [refs](../DataModels/DevLakeDomainLayerSchema.md/#refs)                                     |    |      | ✅            |                  |        |        |         |      | ✅       |      |           |        |             |          |          |
| [refs_issues_diffs](../DataModels/DevLakeDomainLayerSchema.md/#refs_issues_diffs)           |    |      |              |                  |        |        |         |      | ✅       |      |           |        |             |          |          |
| [ref_pr_cherry_picks](../DataModels/DevLakeDomainLayerSchema.md/#ref_pr_cherry_picks)       |    |      |              |                  |        |        |         |      | ✅       |      |           |        |             |          |          |
| [repo_commits](../DataModels/DevLakeDomainLayerSchema.md/#repo_commits)                     |    |      | ✅            |                  | 💪     | 💪     |         |      |         |      |           |        |             |          |          |
| [repo_snapshot](../DataModels/DevLakeDomainLayerSchema.md/#repo_snapshot)                   |    |      | ✅            |                  |        |        |         |      |         |      |           |        |             |          |          |
| [repos](../DataModels/DevLakeDomainLayerSchema.md/#repos)                                   |    |      |              |                  | ✅      | ✅      |         |      |         |      |           |        |             |          |          |
| [sprint_issues](../DataModels/DevLakeDomainLayerSchema.md/#sprint_issues)                   |    |      |              |                  | ✅      |        |         | ✅    |         | ✅    |           |        |             |          |          |
| [sprints](../DataModels/DevLakeDomainLayerSchema.md/#sprints)                               |    |      |              |                  | ✅      |        |         | ✅    |         | ✅    |           |        |             |          |          |
| [team_users](../DataModels/DevLakeDomainLayerSchema.md/#team_users)                         |    |      |              |                  |        |        |         |      |         |      |           |        |             |          |          |
| [teams](../DataModels/DevLakeDomainLayerSchema.md/#teams)                                   |    |      |              |                  |        |        |         |      |         |      |           |        |             | ✅        |          |
| [user_account](../DataModels/DevLakeDomainLayerSchema.md/#user_accounts)                    |    |      |              |                  |        |        |         |      |         |      |           |        |             |          |          |
| [users](../DataModels/DevLakeDomainLayerSchema.md/#users)                                   |    |      |              |                  |        |        |         | ✅    |         | ✅    |           |        |             | ✅        |          |
| [cq_projects](../DataModels/DevLakeDomainLayerSchema.md/#cq_projects)                       |    |      |              |                  |        |        |         |      |         |      | ✅         |        |             |          |          |
| [cq_issues](../DataModels/DevLakeDomainLayerSchema.md/#cq_issues)                           |    |      |              |                  |        |        |         |      |         |      | ✅         |        |             |          |          |
| [cq_issue_code_blocks](../DataModels/DevLakeDomainLayerSchema.md/#cq_issue_code_blocks)     |    |      |              |                  |        |        |         |      |         |      | ✅         |        |             |          |          |
| [cq_file_metrics](../DataModels/DevLakeDomainLayerSchema.md/#cq_file_metrics)               |    |      |              |                  |        |        |         |      |         |      | ✅         |        |             |          |          |

## Data Sync Policy

**bold:** means it may collect slowly.

**\*bold\*:** means it may collect very slowly.

### Jira

| Subtask Name               | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|----------------------------|---------------------------------|-----------------------------------------|------------------------------|
| CollectStatusMeta          | 1                               | -                                       | -                            |
| CollectProjectsMeta        | <10                             | ❌                                       | -                            |
| CollectIssueTypesMeta      | <10                             | ❌                                       | -                            |
| CollectIssuesMeta          | <10^4                           | ✅                                       | ✅                            |
| CollectIssueChangelogsMeta | 1000~10^5                       | ✅                                       | ✅                            |
| CollectAccountsMeta        | <10^3                           | ❌                                       | ❌                            |
| CollectWorklogsMeta        | 1000~10^5                       | ✅                                       | ✅                            |
| CollectRemotelinksMeta     | 1000~10^5                       | ✅                                       | ✅                            |
| CollectSprintsMeta         | <100                            | ❌                                       | ❌                            |
| CollectEpicsMeta           | <100                            | ❌                                       | ✅                            |

### Jenkins

| Subtask Name         | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|----------------------|---------------------------------|-----------------------------------------|------------------------------|
| CollectApiBuildsMeta | ≈100                            | ❌                                       | ❌                            |
| CollectApiStagesMeta | ≈10^4                           | ❌                                       | ✅                            |

### GitLab

| Subtask Name                | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|-----------------------------|---------------------------------|-----------------------------------------|------------------------------|
| CollectApiIssuesMeta        | <10^4                           | ✅                                       | ✅                            |
| CollectApiMergeRequestsMeta | <10^3                           | ✅                                       | ✅                            |
| CollectApiMrNotesMeta       | <10^5                           | ❌                                       | ✅                            |
| CollectApiMrCommitsMeta     | <10^5                           | ❌                                       | ✅                            |
| **CollectApiPipelinesMeta** | <10^4                           | ✅                                       | ❌                            |
| CollectApiJobsMeta          | <10^5                           | ❌                                       | ✅                            |

### Github

| Subtask Name                       | Estimated Max Number of Request     | Does It support Incremental Collection? | Does It Support Time Filter? |
|------------------------------------|-------------------------------------|-----------------------------------------|------------------------------|
| ---------------------------------  | Common                              | -----------------------                 |                              |
| CollectMilestonesMeta              | ≈10                                 | ✅                                       | ❌                            |
| CollectRunsMeta                    | <10^4                               | ✅                                       | ✅                            |
| CollectApiCommentsMeta             | 400 (max page that GitHub supports) | ✅                                       | ✅                            |
| **CollectApiEventsMeta**           | 400 (max page that GitHub supports) | ❌                                       | ❌                            |
| CollectApiPullRequestReviewsMeta   | <10^5                               | ✅                                       | ✅                            |
| ---------------------------------  | Graphql Only (Default)              | -----------------------                 |                              |
| CollectIssueMeta                   | ≈10^4                               | ❌                                       | ✅                            |
| CollectPrMeta                      | ≈10^3                               | ❌                                       | ✅                            |
| CollectCheckRunMeta                | <10^4                               | ❌                                       | ✅                            |
| CollectAccountMeta                 | ≈10^2                               | ❌                                       | -                            |
| ---------------------------------  | Restful Only (Not by Default)       | -----------------------                 |                              |
| CollectApiIssuesMeta               | ≈10^4                               | ✅                                       | ❌                            |
| CollectApiPullRequestsMeta         | ≈10^2                               | ❌                                       | ❌                            |
| CollectApiPullRequestCommitsMeta   | ≈10^4                               | ✅                                       | ✅                            |
| **CollectApiPrReviewCommentsMeta** | ≈10^4                               | ✅                                       | ✅                            |
| **CollectAccountsMeta**            | ≈10^4                               | ❌                                       | ❌                            |
| **CollectAccountOrgMeta**          | ≈10^4                               | ❌                                       | ❌                            |
| CollectJobsMeta                    | <10^6                               | ❌                                       | ✅                            |
| CollectApiCommitsMeta              | Not enabled                         | -                                       | -                            |
| CollectApiCommitStatsMeta          | Not enabled                         | -                                       | -                            |

### Feishu

| Subtask Name                  | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|-------------------------------|---------------------------------|-----------------------------------------|------------------------------|
| CollectMeetingTopUserItemMeta | ≈10^3                           | ❌                                       | ✅                            |

### Bitbucket

| Subtask Name                        | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|-------------------------------------|---------------------------------|-----------------------------------------|------------------------------|
| ~~CollectApiRepoMeta~~              | 1                               | ❌                                       | ❌                            |
| CollectApiPullRequestsMeta          | ≈10^3                           | ❌                                       | ❌                            |
| **CollectApiIssuesMeta**            | ≈10^4                           | ❌                                       | ❌                            |
| **CollectApiPrCommentsMeta**        | ≈10^5                           | ❌                                       | ❌                            |
| **\*CollectApiIssueCommentsMeta\*** | ≈10^6                           | ❌                                       | ❌                            |
| **CollectApiPipelinesMeta**         | <10^4                           | ❌                                       | ❌                            |
| CollectApiDeploymentsMeta           | <10^2                           | ❌                                       | ❌                            |

### Bitbucket Server/Data Center

| Subtask Name                        | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
| ----------------------------------- | ------------------------------- | --------------------------------------- | ---------------------------- |
| ~~CollectApiBranchesMeta~~          | 1                               | ❌                                      | ❌                           |
| CollectApiPullRequestsMeta          | ≈10^3                           | ❌                                      | ❌                           |
| **CollectApiPrActivitiesMeta**      | ≈10^4                           | ❌                                      | ❌                           |
| **CollectApiPrCommentsMeta**        | ≈10^5                           | ❌                                      | ❌                           |
| **\*CollectApiIssueCommentsMeta\*** | ≈10^6                           | ❌                                      | ❌                           |
| **CollectApiCommitsMeta**           | <10^4                           | ❌                                      | ❌                           |
| CollectApiPrCommitsMeta             | <10^2                           | ❌                                      | ❌                           |

### Gitee

| Subtask Name                         | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|--------------------------------------|---------------------------------|-----------------------------------------|------------------------------|
| ~~CollectApiRepoMeta~~               | 1                               | ❌                                       | ❌                            |
| CollectApiPullRequestsMeta           | ≈10^3                           | ✅                                       | ❌                            |
| **CollectApiIssuesMeta**             | ≈10^4                           | ✅                                       | ❌                            |
| **CollectCommitsMeta?**              | ≈10^4                           | ✅                                       | ❌                            |
| **CollectApiPrCommentsMeta**         | ≈10^5                           | ❌                                       | ❌                            |
| **\*CollectApiIssueCommentsMeta\***  | ≈10^6                           | ✅                                       | ❌                            |
| **CollectApiPullRequestCommitsMeta** | ≈10^5                           | ❌                                       | ❌                            |
| **CollectApiPullRequestReviewsMeta** | ≈10^5                           | ❌                                       | ❌                            |
| **\*CollectApiCommitStatsMeta\***    | ≈10^6 (Not enable)              | ❌                                       | ❌                            |

### SonarQube

| Subtask Name                 | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|------------------------------|---------------------------------|-----------------------------------------|------------------------------|
| CollectAccounts              | <10^4                           | ❌                                       | ❌                            |
| CollectIssues                | <10^4                           | ❌                                       | ❌                            |
| CollectHotspots              | <10^4                           | ❌                                       | ❌                            |
| CollectFilemetrics           | <10^4                           | ❌                                       | ❌                            |
| CollectAdditionalFilemetrics | <10^4                           | ❌                                       | ❌                            |

### Bamboo

| Subtask Name         | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|----------------------|---------------------------------|-----------------------------------------|------------------------------|
| CollectPlanMeta      | <10^4                           | ❌                                       | ❌                            |
| CollectJobMeta       | <10^5                           | ❌                                       | ❌                            |
| CollectPlanBuildMeta | <10^6                           | ❌                                       | ❌                            |
| CollectJobBuildMeta  | <10^6                           | ❌                                       | ❌                            |
| CollectDeployMeta    | 1                               | ❌                                       | ❌                            |

### Zentao

| Subtask Name          | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|-----------------------|---------------------------------|-----------------------------------------|------------------------------|
| CollectExecutionMeta  | <10^3                           | ❌                                       | ❌                            |
| CollectStoryMeta      | <10^4                           | ❌                                       | ❌                            |
| CollectBugMeta        | <10^4                           | ❌                                       | ❌                            |
| CollectTaskMeta       | <10^4                           | ❌                                       | ❌                            |
| CollectAccountMeta    | ≈10^2                           | ❌                                       | ❌                            |
| CollectDepartmentMeta | ≈10^2                           | ❌                                       | ❌                            |

### Tapd

| Subtask Name                   | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|--------------------------------|---------------------------------|-----------------------------------------|------------------------------|
| CollectWorkitemTypesMeta       | ≈10                             | -                                       | -                            |
| CollectStoryCustomFieldsMeta   | ≈10                             | -                                       | -                            |
| CollectTaskCustomFieldsMeta    | ≈10                             | -                                       | -                            |
| CollectBugCustomFieldsMeta     | ≈10                             | -                                       | -                            |
| CollectStoryCategoriesMeta     | ≈10                             | -                                       | -                            |
| CollectStoryStatusMeta         | ≈10                             | -                                       | -                            |
| CollectStoryStatusLastStepMeta | ≈10                             | -                                       | -                            |
| CollectBugStatusMeta           | ≈10                             | -                                       | -                            |
| CollectBugStatusLastStepMeta   | ≈10                             | -                                       | -                            |
| CollectAccountsMeta            | ≈10^3                           | ❌                                       | ❌                            |
| CollectIterationMeta           | ≈10^4                           | ✅                                       | ✅                            |
| CollectStoryMeta               | ≈10^4                           | ✅                                       | ✅                            |
| CollectBugMeta                 | ≈10^4                           | ✅                                       | ✅                            |
| CollectTaskMeta                | ≈10^4                           | ✅                                       | ✅                            |
| CollectBugChangelogMeta        | ≈10^6                           | ✅                                       | ✅                            |
| CollectStoryChangelogMeta      | ≈10^6                           | ✅                                       | ✅                            |
| CollectTaskChangelogMeta       | ≈10^6                           | ✅                                       | ✅                            |
| CollectWorklogMeta             | ≈10^6                           | ✅                                       | ✅                            |
| CollectBugCommitMeta           | ≈10^6                           | ✅                                       | ✅                            |
| CollectStoryCommitMeta         | ≈10^6                           | ✅                                       | ✅                            |
| CollectTaskCommitMeta          | ≈10^6                           | ✅                                       | ✅                            |
| CollectStoryBugMeta            | ≈10^6                           | ✅                                       | ✅                            |

### Azure Devops

| Subtask Name              | Estimated Max Number of Request | Does It support Incremental Collection? | Does It Support Time Filter? |
|---------------------------|---------------------------------|-----------------------------------------|------------------------------|
| CollectBuilds             | <10^3                           | ❌                                       | ❌                            |
| CollectJobs               | <10^4                           | ❌                                       | ❌                            |
| CollectPullRequests       | <10^3                           | ❌                                       | ❌                            |
| CollectPullRequestCommits | <10^4                           | ❌                                       | ❌                            |
