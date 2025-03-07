---
api_name: timescaledb_pre_restore()
excerpt: Prepare a database for data restoration
topics: [administration]
keywords: [admin]
tags: [restore, backup, background workers]
api:
  license: apache
  type: function
---

# timescaledb_pre_restore()

Perform the proper operations to allow restoring of the database via `pg_restore` to commence.
Specifically this sets the `timescaledb.restoring` GUC to `on` and stops any
background workers which may have been performing tasks until the [`timescaledb_post_restore`][timescaledb_post_restore]
function is run following the restore. See [backup/restore docs][backup-restore] for more information.

<Highlight type="warning">
 Using this function when doing an upgrade could cause issues in TimescaleDB
 versions before 1.7.1.

</Highlight>

<Highlight type="warning">
 After running `SELECT timescaledb_pre_restore()` you must run the
  [`timescaledb_post_restore`](/api/latest/administration/timescaledb_post_restore/) function before using
  the database normally.

</Highlight>

### Sample usage  

```sql
SELECT timescaledb_pre_restore();
```

[backup-restore]: /timescaledb/:currentVersion:/how-to-guides/backup-and-restore/pg-dump-and-restore/
[timescaledb_post_restore]: /api/:currentVersion:/administration/timescaledb_post_restore/
