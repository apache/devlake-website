---
title: "Grafana IRM"
sidebar_position: 18
description: Config UI instruction for Grafana IRM
---

Visit Config UI at: `http://localhost:4000`.

## Step 1 - Add Data Connections

### Step 1.1 - Authentication

#### Connection Name

Give your connection a unique name to help you identify it in the future.

#### Endpoint URL

The base URL of your Grafana Cloud stack:
```
https://<your-org-name>.grafana.net/
```
The plugin calls the Grafana IRM API via `/api/plugins/grafana-incident-app/resources/api/v1/incidents`.

#### Token

Paste your Grafana Cloud **Service Account Token** or **API Key** here. The token requires read permissions for incidents (`incidents:read`).

#### Rate Limit (Optional)

By default, DevLake uses 3,600 requests/hour for data collection for Grafana IRM. You can adjust the collection speed by setting up your desirable rate limit.

#### Test and Save Connection

Click `Test Connection`, if the connection is successful, click `Save Connection` to add the connection.

### Step 1.2 - Add Data Scopes

Select the scope for incident collection (by default, `default` organization scope).

Only Grafana IRM incidents will be collected. The data will be stored in the `issues`, `board_issues` and `boards` tables with issue type `INCIDENT`, feeding the DORA change failure rate and failed deployment recovery time (median time to restore service) metrics.

The incident's `created_at` timestamp drives the created date, `updated_at` drives incremental synchronization, and `resolved_at` drives the resolution date. Timestamps are truncated to millisecond precision for deterministic database storage.

Note: the Grafana IRM plugin does not support any scope config.

## Step 2 - Collect Grafana IRM Data in a Project

### Step 2.1 - Create a Project

Collecting Grafana IRM data requires creating a project first.

Navigate to the **Projects** page from the side menu and create a new project.

### Step 2.2 - Add a Grafana IRM Connection

In the project, add the Grafana IRM connection and select its data scopes.

Please note: if you don't see the scope you are looking for, please check if you have added it to the connection first.

### Step 2.3 - Set the Sync Policy (Optional)

There are three settings for Sync Policy:
- Data Time Range: You can select the time range of the data you wish to collect. The default is set to the past six months.
- Sync Frequency: You can choose how often you would like to sync your data in this step by selecting a sync frequency option or enter a cron code to specify your preferred schedule.
- Skip Failed Tasks: sometimes a few tasks may fail in a long pipeline; you can choose to skip them to avoid spending more time in running the pipeline all over again.

### Step 2.4 - Start Data Collection

Click on **Collect Data** to start collecting data for the whole project.

## Troubleshooting

If you run into any problem, please check the [Troubleshooting](/Troubleshooting/Configuration.md) or [create an issue](https://github.com/apache/devlake/issues)
