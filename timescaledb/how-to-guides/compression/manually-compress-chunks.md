---
title: Manual compression
excerpt: Manually compress a hypertable
keywords: [compression, hypertables]
---

# Manual compression

In most cases, an automated compression policy is sufficient. However, if you
want more control over compression, you can also manually compress specific
chunks.

<Highlight type="warning">
Compression alters data on your disk, so always back up before you start.
</Highlight>

## Compress chunks manually

Before you start, you need a list of chunks to compress. In this example, you
use a hypertable called `example`, and compress chunks older than three days.

<Procedure>

### Selecting chunks to compress

1.  At the psql prompt, select all chunks in the table `example` that are older
    than three days:

    ```sql
    SELECT show_chunks('example', older_than => INTERVAL '3 days');
    ```

1.  This returns a list of chunks. Take a note of the chunk names:

    ||show_chunks|
    |---|---|
    |1|_timescaledb_internal_hyper_1_2_chunk|
    |2|_timescaledb_internal_hyper_1_3_chunk|

</Procedure>

When you are happy with the list of chunks, you can use the chunk names to
manually compress each one.

<Procedure>

### Compressing chunks manually

1.  At the psql prompt, compress the chunk:

    ```sql
    SELECT compress_chunk( '<chunk_name>');
    ```

1.  Check the results of the compression with this command:

    ```sql
    SELECT *
    FROM chunk_compression_stats('example');
    ```

    The results show the chunks for the given hypertable, their compression
    status, and some other statistics:

    |chunk_schema|chunk_name|compression_status|before_compression_table_bytes|before_compression_index_bytes|before_compression_toast_bytes|before_compression_total_bytes|after_compression_table_bytes|after_compression_index_bytes|after_compression_toast_bytes|after_compression_total_bytes|node_name|
    |---|---|---|---|---|---|---|---|---|---|---|---|
    |_timescaledb_internal|_hyper_1_1_chunk|Compressed|8192 bytes|16 kB|8192 bytes|32 kB|8192 bytes|16 kB|8192 bytes|32 kB||
    |_timescaledb_internal|_hyper_1_20_chunk|Uncompressed||||||||||

1.  Repeat for all chunks you want to compress.

</Procedure>

## Manually compress chunks in a single command

Alternatively, you can select the chunks and compress them in a single command
by using the output of the `show_chunks` command to compress each one. For
example, use this command to compress chunks between one and three weeks old
if they are not already compressed:

```sql
SELECT compress_chunk(i, if_not_compressed => true)
    FROM show_chunks(
        'example',
        now()::timestamp - INTERVAL '1 week',
        now()::timestamp - INTERVAL '3 weeks'
    ) i;
```

## Roll up uncompressed chunks when compressing

In TimescaleDB 2.9 and later, you can roll up multiple uncompressed chunks into
a previously compressed chunk as part of your compression procedure. This allows
you to have much smaller uncompressed chunk intervals, which reduces the disk
space used for uncompressed data. For example, if you have multiple smaller
uncompressed chunks in your data, you can roll them up into a single compressed
chunk.

To roll up your uncompressed chunks into a compressed chunk, use this command,
specifying the interval to use:

```sql
SELECT compress_chunk_time_interval('<time_interval>',if_not_compressed => true)
    FROM show_chunks(
        'example',
        now()::timestamp - INTERVAL '1 week',
    ) ;
```

The time interval you choose must be a multiple of the uncompressed chunk
interval. For example, if your uncompressed chunk interval is one week, your
`<time_interval>` of the compressed chunk could be two weeks, or six weeks, but
not one month.
