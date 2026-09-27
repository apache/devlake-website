---
title: "Grafana IRM"
description: >
  Grafana IRM Plugin
---

## Summary

This plugin collects incident data from Grafana Cloud Incident Response & Management (IRM), and uses them to compute incident-type DORA metrics. Namely,
* [Median time to restore service](/Metrics/MTTR.md)
* [Change failure rate](/Metrics/CFR.md).
* [Incident Age](/Metrics/IncidentAge.md)

## Supported Versions
Available for Grafana Cloud IRM. Check [this doc](https://devlake.apache.org/docs/Overview/SupportedDataSources#data-sources-and-data-plugins) for more details.


## Configuration
* Configure Grafana IRM via Config UI. See instructions [here](/Configuration/GrafanaIrm.md).
* Configure Grafana IRM via Config UI's [advanced mode](/Configuration/AdvancedMode.md).
