---
api_name: alter_job()
excerpt: Alter a job that is scheduled to run automatically
topics: [jobs]
keywords: [jobs]
tags: [scheduled jobs, user-defined actions, automation framework, background jobs, alter, change]
api:
  license: community
  type: function
---

# alter_job() <Tag type="community">Community</Tag>

Actions scheduled using the TimescaleDB automation framework run periodically in
a background worker. You can change the schedule of these jobs with the
`alter_job` function. To alter an existing job, refer to it by `job_id`. The
`job_id` runs a given action, and its current schedule can be found in the
`timescaledb_information.jobs` view, which lists information about every
scheduled action, as well as in `timescaledb_information.job_stats`. The
`job_stats` view also gives information about when each job was last run and
other useful statistics for deciding what the new schedule should be.

### Required arguments

|Name|Type|Description|
|-|-|-|
|`job_id`|`INTEGER`|The ID of the policy job being modified|

### Optional arguments

|Name|Type|Description|
|-|-|-|
|`schedule_interval`|`INTERVAL`|The interval at which the job runs. Defaults to 24 hours|
|`max_runtime`|`INTERVAL`|The maximum amount of time the job is allowed to run by the background worker scheduler before it is stopped|
|`max_retries`|`INTEGER`|The number of times the job is retried if it fails|
|`retry_period`|`INTERVAL`|The amount of time the scheduler waits between retries of the job on failure|
|`scheduled`|`BOOLEAN`|Set to `FALSE` to exclude this job from being run as background job|
|`config`|`JSONB`|Job-specific configuration, passed to the function when it runs|
|`next_start`|`TIMESTAMPTZ`|The next time at which to run the job. The job can be paused by setting this value to `infinity`, and restarted with a value of `now()`|
|`if_exists`|`BOOLEAN`|Set to `true`to issue a notice instead of an error if the job does not exist. Defaults to false.|
|`check_config`|`REGPROC`|A function that takes a single argument, the `JSONB` `config` structure. The function is expected to raise an error if the configuration is not valid, and return nothing otherwise. Can be used to validate the configuration when updating a job. Only functions, not procedures, are allowed as values for `check_config`.|

When a job begins, the `next_start` parameter is set to `infinity`. This
prevents the job from attempting to be started again while it is running. When
the job completes, whether or not the job is successful, the parameter is
automatically updated to the next computed start time.

Note that, the optional parameters `initial_start` and `timezone` are not
modifiable with `alter_job`. It is not currently possible to alter these
parameters for an existing job. If you wish to do so, you must drop the
existing job and register it again with the new values. It is also not possible
to change the value of the `fixed_schedule` parameter. You must drop and
recreate the job if you wish to alter this behavior.
Also note that altering the `next_start` value is only effective for the next
execution of the job in case of fixed schedules. On the next execution, it will
automatically return to the schedule.

### Returns

|Column|Type|Description|
|-|-|-|
|`job_id`|`INTEGER`|The ID of the job being modified|
|`schedule_interval`|`INTERVAL`|The interval at which the job runs. Defaults to 24 hours|
|`max_runtime`|`INTERVAL`|The maximum amount of time the job is allowed to run by the background worker scheduler before it is stopped|
|`max_retries`|INTEGER|The number of times the job is retried if it fails|
|`retry_period`|`INTERVAL`|The amount of time the scheduler waits between retries of the job on failure|
|`scheduled`|`BOOLEAN`|Returns `true` if the job is executed by the TimescaleDB scheduler|
|`config`|`JSONB`|Job-specific configuration, passed to the function when it runs|
|`next_start`|`TIMESTAMPTZ`|The next time to run the job|
|`check_config`|`TEXT`|The function used to validate updated job configurations|

### Sample usage

Reschedules job ID `1000` so that it runs every two days:

```sql
SELECT alter_job(1000, schedule_interval => INTERVAL '2 days');
```

Disables scheduling of the compression policy on the `conditions` hypertable:

```sql
SELECT alter_job(job_id, scheduled => false)
FROM timescaledb_information.jobs
WHERE proc_name = 'policy_compression' AND hypertable_name = 'conditions'
```

Reschedules continuous aggregate job ID `1000` so that it next runs at 9:00:00 on 15 March, 2020:

```sql
SELECT alter_job(1000, next_start => '2020-03-15 09:00:00.0+00');
```
