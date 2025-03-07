---
title: Ingest data
excerpt: Ingest data into Managed Service for Timescaledb
product: mst
keywords: [ingest, data migration]
tags: [JDB, ODBC, client driver, Kafka, csv]
---

# Ingest data

There are several different ways of ingesting your data into Managed Service for
TimescaleDB. This section contains instructions to:

*   Bulk upload [from a `.csv` file](#bulk-upload-from-csv-files)
*   Migrate data [from an existing database][migrate-data]
*   Migrate data [from InfluxDB][migrate-influxdb]
*   Insert data
    [directly using a client driver](#insert-data-directly-using-a-client-driver),
    such as JDBC, ODBC, or Node.js
*   Insert data
    [directly using a message queue](#insert-data-directly-using-a-message-queue),
    such as Kafka

<Highlight type="note">
Before you begin, make sure you have
[created your Managed Service for TimescaleDB service](/mst/latest/create-a-service/), and
can connect to it using `psql`.
</Highlight>

<Procedure>

## Preparing your new database

1.  Use `psql` to connect to your service. You can retrieve the service URL,
    port, and login credentials from the service overview in the Timescale Cloud dashboard:

    ```sql
    psql -h <HOSTNAME> -p <PORT> -U <USERNAME> -W -d <DATABASE_NAME>
    ```

1.  Create a new database for your data. In this example, the new database is
    called `new_db`:

    ```sql
    CREATE DATABASE new_db;
    \c new_db;
    ```

1.  Create a new SQL table in your database. The columns you create for the
    table must match the columns in your source data. In this example, the table
    is storing weather condition data, and has columns for the timestamp,
    location, and temperature:

    ```sql
    CREATE TABLE conditions (
      time        TIMESTAMPTZ         NOT NULL,
      location    text                NOT NULL,
      temperature DOUBLE PRECISION    NULL
    );
    ```

1.  Load the `timescaledb` PostgreSQL extension:

    ```sql
    CREATE EXTENSION timescaledb;
    \dx
    ```

1.  Convert the SQL table into a hypertable:

    ```sql
    SELECT create_hypertable('conditions', 'time');
    ```

</Procedure>

When you have successfully set up your new database, you can ingest data using
one of these methods.

## Bulk upload from CSV files

If you have a dataset stored in a `.csv` file, you can import it into an empty
TimescaleDB hypertable. You need to begin by creating the new table, before you
import the data.

<Highlight type="important">
Before you begin, make sure you have
[prepared your new database](#procedure-preparing-your-new-database).
</Highlight>

<Procedure>

### Bulk uploading from a CSV file

1.  Insert data into the new hypertable using the `timescaledb-parallel-copy`
    tool. You should already have the tool installed, but you can install it
    manually from [our GitHub repository][github-parallel-copy] if you need to.
    In this example, we are inserting the data using four workers:

    ```sql
    timescaledb-parallel-copy \
    --connection '<service_url>' \
    --table conditions \
    --file ~/Downloads/example.csv \
    --workers 4 \
    --copy-options "CSV" \
    --skip-header
    ```

    We recommend that you set the number of workers lower than the number of
    available CPU cores on your client machine or server, to prevent the workers
    having to compete for resources. This helps your ingest go faster.
1.  *OPTIONAL:* If you don't want to use the `timescaledb-parallel-copy` tool,
    or if you have a very small dataset, you can use the PostgreSQL `COPY`
    command instead:

    ```sql
    psql '<service_url>/new_db?sslmode=require' -c "\copy conditions FROM <example.csv> WITH (FORMAT CSV, HEADER)"
    ```

</Procedure>

## Insert data directly using a client driver

You can use a client driver such as JDBC, Python, or Node.js, to insert data
directly into your new database.

See the [PostgreSQL instructions][postgres-odbc] for using the ODBC driver.

See the [Code Quick Starts][code-qs] for using various languages, including Python and node.js.

## Insert data directly using a message queue

If you have data stored in a message queue, you can import it into your
TimescaleDB database. This section provides instructions on using the Kafka
Connect PostgreSQL connector.

This connector deploys PostgreSQL change events from Kafka Connect to a runtime
service. It monitors one or more schemas in a TimescaleDB server, and writes all
change events to Kafka topics, which can then be independently consumed by one
or more clients. Kafka Connect can be distributed to provide fault tolerance,
which ensures the connectors are running and continually keeping up with changes
in the database.

You can also use the PostgreSQL connector as a library without Kafka or Kafka
Connect. This allows applications and services to directly connect to
TimescaleDB and obtain the ordered change events. In this environment, the
application must record the progress of the connector so that when it is
restarted, the connect can continue where it left off. This approach can be
useful for less critical use cases. However, for production use cases, we
recommend that you use the connector with Kafka and Kafka Connect.

See [these instructions][gh-kafkaconnector] for using the Kafka connector.

[code-qs]: /timescaledb/:currentVersion:/quick-start/
[gh-kafkaconnector]: https://github.com/debezium/debezium/tree/master/debezium-connector-postgres
[github-parallel-copy]: https://github.com/timescale/timescaledb-parallel-copy
[migrate-data]: /timescaledb/:currentVersion:/how-to-guides/migrate-data/
[migrate-influxdb]: /timescaledb/:currentVersion:/how-to-guides/migrate-data/migrate-influxdb/
[postgres-odbc]: https://odbc.postgresql.org/
