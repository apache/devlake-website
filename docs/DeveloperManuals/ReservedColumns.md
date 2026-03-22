---
title: "Reserved Columns"
description: >
  Reserved column names in DevLake's data models and naming conventions for source-system timestamps.
---

# Reserved Columns

## Summary

DevLake's base model structs (`common.NoPKModel` and `common.Model`) automatically manage certain columns. These columns are **reserved** and must not be used to store data originating from external data sources. This page explains which columns are reserved, why they exist, and what naming conventions to follow when your plugin needs to store timestamps from a data source.

## Reserved Column Names

The following columns are reserved by DevLake:

| Column             | Type           | Purpose                                                                             |
| ------------------ | -------------- | ----------------------------------------------------------------------------------- |
| `created_at`       | `datetime(3)`  | Automatically set to the time the record is **inserted** into DevLake's database.   |
| `updated_at`       | `datetime(3)`  | Automatically set to the time the record is last **updated** in DevLake's database. |
| `_raw_data_params` | `varchar(255)` | Links the record back to its raw-layer source.                                      |
| `_raw_data_table`  | `varchar(255)` | Identifies which raw table the record was extracted from.                           |
| `_raw_data_id`     | `bigint`       | References the specific row in the raw table.                                       |

The `created_at` and `updated_at` columns are managed by GORM's `autoCreateTime` and `autoUpdateTime` tags on the `NoPKModel` base struct. They reflect **when DevLake processed the record**, not when the record was created or updated in the original data source.

## Why This Matters

If a plugin maps a data source's creation or update timestamp directly to `created_at` or `updated_at`, the value will be silently overwritten by GORM's auto-timestamp behavior. This means:

- The source-system timestamp is lost.
- The column no longer accurately reflects DevLake's own processing time.
- Downstream queries and dashboards that rely on these columns may produce incorrect results.

## Naming Convention for Source Timestamps

When your plugin model needs to store timestamps from an external data source, use a **prefixed column name** that identifies the source system. Here are examples from existing plugins:

### Go (Tool-Layer Models)

```go
// ✅ Correct — use a source-specific prefix
type GithubIssue struct {
    ConnectionId    uint64    `gorm:"primaryKey"`
    GithubId        int       `gorm:"primaryKey"`
    // ...
    GithubCreatedAt time.Time                     // timestamp from GitHub
    GithubUpdatedAt time.Time `gorm:"index"`      // timestamp from GitHub
    common.NoPKModel                               // provides created_at, updated_at
}

// ❌ Wrong — overwrites DevLake's auto-managed columns
type GithubIssue struct {
    ConnectionId uint64    `gorm:"primaryKey"`
    GithubId     int       `gorm:"primaryKey"`
    CreatedAt    time.Time                        // conflicts with NoPKModel
    UpdatedAt    time.Time                        // conflicts with NoPKModel
    common.NoPKModel
}
```

### Go (Domain-Layer Models)

Domain-layer models follow the same rule. Use descriptive names like `CreatedDate`, `UpdatedDate`, or source-prefixed names:

```go
type GitlabDeployment struct {
    common.NoPKModel
    ConnectionId uint64    `gorm:"primaryKey"`
    GitlabId     int       `gorm:"primaryKey"`
    CreatedDate  time.Time `json:"created_date"`    // from GitLab API
    UpdatedDate  *time.Time `json:"updated_date"`   // from GitLab API
    // ...
}
```

### Python (PyDevLake Models)

The same convention applies to Python plugins. The `NoPKModel` base class in PyDevLake provides `created_at` and `updated_at` automatically:

```python
class MyToolIssue(ToolModel):
    # ✅ Correct
    tool_created_at: datetime    # timestamp from the external tool
    tool_updated_at: datetime    # timestamp from the external tool

    # ❌ Wrong — these conflict with NoPKModel
    # created_at: datetime
    # updated_at: datetime
```

## Common Prefixes Used Across Plugins

| Plugin  | Prefix Pattern                | Example                                     |
| ------- | ----------------------------- | ------------------------------------------- |
| GitHub  | `Github`                      | `GithubCreatedAt`, `GithubUpdatedAt`        |
| GitLab  | `Gitlab`                      | `GitlabCreatedAt`, `GitlabUpdatedAt`        |
| Jira    | `Created`, `Updated`          | `Created`, `Updated` (Jira-specific fields) |
| Generic | `CreatedDate` / `UpdatedDate` | `CreatedDate`, `UpdatedDate`                |

Pick whichever prefix is most natural for your data source, but **never** use the bare names `created_at` / `updated_at` (or their Go equivalents `CreatedAt` / `UpdatedAt` without a prefix).

## Quick Checklist

When defining a new tool-layer or domain-layer model:

1. **Embed `common.NoPKModel`** (Go) or extend `NoPKModel` / `ToolModel` (Python) — this gives you `created_at` and `updated_at` for free.
2. **Do not** add your own `CreatedAt` or `UpdatedAt` fields without a source-specific prefix.
3. **Name source timestamps** with a prefix that identifies the data source (e.g., `GithubCreatedAt`) or use a generic alternative like `CreatedDate`.
4. **Document** the meaning of each timestamp field with a comment or `gorm:"comment:..."` tag so that other contributors understand which system the timestamp originates from.

## References

- Base model definition: [`models/common`](https://github.com/apache/incubator-devlake/tree/main/backend/core/models/common)
- [PR #8701](https://github.com/apache/incubator-devlake/pull/8701) — CircleCI column naming fix that prompted this convention to be formally documented.
