---
title: "YouTrack"
description: >
  YouTrack Plugin
---

## Summary

This plugin collects `YouTrack` projects, issues, comments, issue changelogs, labels and accounts, and converts them into DevLake's TICKET domain layer so they can be used by the issue-tracking metrics and dashboards.

Issues, comments and changelogs are collected incrementally, so a scheduled pipeline only pulls what changed since the last run.

## Supported Versions

Available for YouTrack Cloud, and for self-hosted YouTrack instances through the configurable endpoint. Check [this doc](https://devlake.apache.org/docs/Overview/SupportedDataSources#data-sources-and-data-plugins) for more details.

## Configuration

The connection needs three things:

| Field | Description |
|---|---|
| Endpoint | Base URL of the instance, for example `https://example.youtrack.cloud` |
| Token | A permanent token. It starts with `perm:` — create one under **Profile → Account Security → New Token** |
| Rate Limit Per Hour | Optional throttle for the API |

The token must be able to read the project and its custom fields. A token that silently authenticates as a guest returns empty results rather than an error, which is why the Config UI **Test connection** button warns when that happens.

After the connection is saved, pick the projects to collect as scopes, then map the project's custom fields to DevLake's Type, State, Priority, Assignee, Story Points and Due Date in the scope config. Type, State, Priority and Assignee fall back to YouTrack's default field names when left empty.

## Configuration via API

A connection can also be created directly:

```
curl 'http://localhost:8080/plugins/youtrack/connections' \
--header 'Content-Type: application/json' \
--data-raw '
{
    "name": "youtrack",
    "endpoint": "https://example.youtrack.cloud",
    "token": "perm:<YOUR_TOKEN>",
    "rateLimitPerHour": 10000
}
'
```

See [Advanced mode](/Configuration/AdvancedMode.md) for collecting scopes and scope configs the same way.
