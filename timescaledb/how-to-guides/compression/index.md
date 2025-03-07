---
title: Compression
excerpt: Learn how compression works in TimescaleDB
keywords: [compression, hypertables]
---

# Compression

Time-series data can be compressed to reduce the amount of storage required, and
increase the speed of some queries. This is a cornerstone feature of
TimescaleDB. When new data is added to your database, it is in the form of
uncompressed rows. TimescaleDB uses a built-in job scheduler to convert this
data to the form of compressed columns. This occurs across chunks of TimescaleDB
hypertables.

This section explains what native compression is, and goes through some of the
benefits and limitations of compression. It also includes instructions for
setting up compression, and using it in your environment. We strongly recommend
that you understand how compression works *before* you start enabling it on your
hypertables.

*   [Learn about compression][compression] to understand how it works before you begin using it.
*   [Manually compress][manual-compression] specific chunks.
*   [Decompress chunks][decompress-chunks] to manually decompress specific chunks.
*   [Backfill historical data][backfill-historical] to insert a batch of data into a compressed chunk.
*   [Modify schema][modify-schema] to modify the table definition for a hypertable with compressed chunks.

<Highlight type="warning">
Compression alters data on your disk, so always back up before you start!
</Highlight>

[backfill-historical]: /timescaledb/:currentVersion:/how-to-guides/compression/backfill-historical-data
[compression]: /timescaledb/:currentVersion:/how-to-guides/compression/about-compression
[decompress-chunks]: /timescaledb/:currentVersion:/how-to-guides/compression/decompress-chunks
[manual-compression]: /timescaledb/:currentVersion:/how-to-guides/compression/manually-compress-chunks
[modify-schema]: /timescaledb/:currentVersion:/how-to-guides/compression/modify-a-schema
