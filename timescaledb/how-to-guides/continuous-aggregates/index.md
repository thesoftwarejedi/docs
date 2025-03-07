---
title: Continuous aggregates
excerpt: Continuous aggregates make queries run faster on very large datasets
keywords: [continuous aggregates]
---

# Continuous aggregates

Continuous aggregates are designed to make queries on very large datasets run
faster. TimescaleDB continuous aggregates use
PostgreSQL [materialized views][postgres-materialized-views] to continuously and
incrementally refresh a query in the background, so that when you run the query,
only the data that has changed needs to be computed, not the entire dataset.

*   [Learn about continuous aggregates][about-caggs] to understand how it works
    before you begin using it.
*   [Create a continuous aggregate][cagg-create] and query it.
*   [Create a continuous aggregate on top of another continuous aggregate][cagg-on-cagg]
*   [Add refresh policies][cagg-autorefresh] to an existing continuous aggregate.
*   [Manage time][cagg-time] in your continuous aggregates.
*   [Drop data][cagg-drop] from your continuous aggregates.
*   [Manage materialized hypertables][cagg-mat-hypertables].
*   [Use real-time aggregates][cagg-realtime].
*   [Compression with continuous aggregates][cagg-compression].
*   [Migrate your continuous aggregates][cagg-migrate] from old to new format.
    Continuous aggregates created in TimescaleDB 2.7 and above are in the new
    format, unless explicitly created in the old format.
*   [Troubleshoot][cagg-tshoot] continuous aggregates.

[about-caggs]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/about-continuous-aggregates
[cagg-autorefresh]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/refresh-policies
[cagg-compression]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/compression-on-continuous-aggregates
[cagg-create]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/create-a-continuous-aggregate
[cagg-on-cagg]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/hierarchical-continuous-aggregates/
[cagg-drop]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/drop-data
[cagg-mat-hypertables]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/materialized-hypertables
[cagg-migrate]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/migrate
[cagg-realtime]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/real-time-aggregates
[cagg-time]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/time
[cagg-tshoot]: /timescaledb/:currentVersion:/how-to-guides/continuous-aggregates/troubleshooting
[postgres-materialized-views]: https://www.postgresql.org/docs/current/rules-materializedviews.html
