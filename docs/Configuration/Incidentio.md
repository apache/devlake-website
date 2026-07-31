---
title: "incident.io"
sidebar_position: 18
description: Config UI instruction for incident.io
---

Visit Config UI at: `http://localhost:4000`.

## Step 1 - Add Data Connections

### Step 1.1 - Authentication

#### Connection Name

Give your connection a unique name to help you identify it in the future.

#### Endpoint URL

The default endpoint is `https://api.incident.io/`.

#### Token

Paste your incident.io API key here. You can create one in incident.io under **Settings → API keys**; read-only scopes for incidents, incident types, and severities are sufficient for the plugin's purposes.

#### Test and Save Connection

Click `Test Connection`, if the connection is successful, click `Save Connection` to add the connection.

### Step 1.2 - Add Data Scopes

Choose the **incident types** to collect. Most workspaces have a single incident type named `Default`.

Only incident.io incidents will be collected. Incidents in `test` or `tutorial` mode are excluded automatically. The data will be stored in the `issues`, `board_issues` and `boards` tables with issue type `INCIDENT`, feeding the DORA change failure rate and failed deployment recovery time metrics.

The incident's `Declared at` timestamp drives the created date and `Resolved at` (falling back to `Closed at`) drives the resolution date. Retrospective incidents — declared after they were resolved — keep their resolution date; only the declared-to-resolved lead time is omitted for them.

Note: the incident.io plugin does not support any scope config.

## Step 2 - Collect incident.io Data in a Project

### Step 2.1 - Create a Project

Collecting incident.io data requires creating a project first.

Navigate to the **Projects** page from the side menu and create a new project.

### Step 2.2 - Add an incident.io Connection

In the project, add the incident.io connection and select its data scopes.

Please note: if you don't see the incident types you are looking for, please check if you have added them to the connection first.

### Step 2.3 - Set the Sync Policy (Optional)

There are three settings for Sync Policy:
- Data Time Range: You can select the time range of the data you wish to collect. The default is set to the past six months.
- Sync Frequency: You can choose how often you would like to sync your data in this step by selecting a sync frequency option or enter a cron code to specify your preferred schedule.
- Skip Failed Tasks: sometimes a few tasks may fail in a long pipeline; you can choose to skip them to avoid spending more time in running the pipeline all over again.

### Step 2.4 - Start Data Collection

Click on **Collect Data** to start collecting data for the whole project.

## Troubleshooting

If you run into any problem, please check the [Troubleshooting](/Troubleshooting/Configuration.md) or [create an issue](https://github.com/apache/incubator-devlake/issues)
