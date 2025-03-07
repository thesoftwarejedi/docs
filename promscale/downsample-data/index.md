---
title: Downsample metric data in Promscale
excerpt: Configure downsampling in Promscale
product: promscale
keywords: [Prometheus, downsample]
tags: [continuous aggregates, metrics, recording rules]
---

import PromscaleDeprecation from "versionContent/_partials/_deprecated-promscale.mdx";

# Downsample metric data in Promscale

<PromscaleDeprecation />

Downsampling is the term used when reducing the rate of a signal. When you
downsample data, the size of the data on disk is reduced, but so is the
resolution of the data. This means that you have fewer entries in your source
data, to take up less space on disk. The main reasons for downsampling are to
reduce cost and increase performance. Querying downsampled data gets faster as
the size of the data decreases.

The two downsampling methods are:

*   [PromQL based recording rules][recording] offered by Prometheus
*   [Continuous aggregates][caggs] offered by TimescaleDB

## Choose a downsampling method

There are a few things to take into account when deciding on a downsampling
method:

*   Access to recent data: if this materialization is being used in operational
    or real-time dashboards, choose continuous aggregates.
*   Size of the time bucket: continuous aggregates materialize the intermediate,
    not the final form, so querying the data is a bit more expensive than with
    recording rules. Choose continuous aggregates if you are aggregating more
    data points together, such as one hour or more of data. Choose recording
    rules for small buckets.
*   Number of metrics in materialization: continuous aggregates can only be
    defined on a single metric. If you need to materialize queries on more than
    one metric, choose recording rules. However, if you want to use continuous
    aggregates, you could consider joining the materialized metrics and querying
    the result of the materialization instead of the raw input.
*   Query flexibility: if you know the exact queries that you want to run on the
    materialization, recording rules can be more efficient. However, if you want
    flexibility in your queries, continuous aggregates can answer more queries
    based on the materialized data.
*   Access to old data: if you need old data points to also be aggregated as
    soon as downsampling for a metric is configured, choose continuous
    aggregates. This is especially important if it is something you do often,
    because backfilling old data with recording rules requires additional steps.

[caggs]: /promscale/:currentVersion:/downsample-data/caggs/
[recording]: /promscale/:currentVersion:/downsample-data/recording/
