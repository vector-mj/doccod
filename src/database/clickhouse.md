
### Get information about replicas
```sql
select 
  database,
  table,
  is_leader,
  is_readonly,
  total_replicas,
  active_replicas,
  replica_is_active,
  replica_name
  from cluster('{cluster}', system.replicas);
```

### Sync Clickhouse metadata with Zookeeper
```sql
system restart replica ON cluster '{cluster}'  <DB>.<TABLE>
system restore replica ON cluster '{cluster}'  <DB>.<TABLE>
```

### Sync a replica with others
```sql
system sync replica on cluster '{cluster}' <DB>.<TABLE>
system sync database replica <DB>;
```

### Check replication status
```sql
SELECT
    database,
    table,
    is_leader,
    is_readonly,
    is_session_expired,
    future_parts,
    parts_to_check,
    columns_version,
    queue_size,
    inserts_in_queue,
    merges_in_queue,
    log_max_index,
    log_pointer,
    total_replicas,
    active_replicas
FROM system.replicas
WHERE
       is_readonly
    OR is_session_expired
    OR future_parts > 20
    OR parts_to_check > 10
    OR queue_size > 20
    OR inserts_in_queue > 10
    OR log_max_index - log_pointer > 10
    OR total_replicas < 2
    OR active_replicas < total_replicas
```

### Tables status
```sql

select parts.*,
       columns.compressed_size,
       columns.uncompressed_size,
       columns.compression_ratio,
       columns.compression_percentage
from (
         select table,
                formatReadableSize(sum(data_uncompressed_bytes))          AS uncompressed_size,
                formatReadableSize(sum(data_compressed_bytes))            AS compressed_size,
                round(sum(data_compressed_bytes) / sum(data_uncompressed_bytes), 3) AS  compression_ratio,
                round((100 - (sum(data_compressed_bytes) * 100) / sum(data_uncompressed_bytes)), 3) AS compression_percentage

             from system.columns
             group by table
         ) columns
         right join (
    select table,
           sum(rows)                                            as rows,
           max(modification_time)                               as latest_modification,
           formatReadableSize(sum(bytes))                       as disk_size,
           formatReadableSize(sum(primary_key_bytes_in_memory)) as primary_keys_size,
           any(engine)                                          as engine,
           sum(bytes)                                           as bytes_size
    from system.parts
    where active 
    group by database, table
    ) parts on columns.table = parts.table
order by parts.bytes_size desc;
```

### Columns size
```sql
SELECT
    database,
    table,
    formatReadableSize(sum(data_compressed_bytes) AS size) AS compressed,
    formatReadableSize(sum(data_uncompressed_bytes) AS usize) AS uncompressed,
    round(usize / size, 2) AS compr_rate,
    sum(rows) AS rows,
    count() AS part_count
FROM system.parts
WHERE (active = 1) AND (database LIKE '%') AND (table LIKE '%')
GROUP BY
    database,
    table
ORDER BY size DESC;
```

### Check corrupted data
```sql
set check_query_single_value_result = 0;
--------------------------------
check table <DB>.<TABLE>;
--------------------------------
CHECK ALL TABLES FORMAT PrettyCompactMonoBlock SETTINGS check_query_single_value_result = 0
```

### Zookeeper connections status
```sql
select * from system.zookeeper_connection;
select * from system.zookeeper where path='/clickhouse/';
```

### From remote replica
```sql
select count() from remote('<REPLICA_NAME>',<DB>.<TABLE>,'<USER>','<PASS>');
```

### Attach and Detach tables
```sql
detach table <DB>.<TABLE> on cluster '{cluster}' sync;
attach table <DB>.<TABLE> on cluster '{cluster}';
```

### To find parts and detached parts
```sql
select * from system.detached_parts;
-------------------------------
select partition_id,name FROM system.parts WHERE database='<DB>';
-------------------------------
select substr(table, 1, 22),partition AS prt,name,part_type,path FROM system.parts WHERE database = '<DB>' ORDER BY table ASC, partition ASC, name ASC
-------------------------------
SELECT *,
       concat('alter table ',database,'.',table,' drop detached part ''',a.name,''' settings allow_drop_detached=1;') as drop
FROM system.detached_parts a
ALL LEFT JOIN
(SELECT database, table, partition_id, name, active, min_block_number, max_block_number
   FROM system.parts WHERE active
) b
USING (database, table, partition_id)
WHERE a.min_block_number >= b.min_block_number
  AND a.max_block_number <= b.max_block_number
ORDER BY table, min_block_number, max_block_number
-------------------------------
select partition,name,active,bytes_on_disk as diskSize,database,table,path from system.parts;
-------------------------------
SELECT database, table, reason, count()
FROM system.detached_parts
GROUP BY database, table, reason
ORDER BY database ASC, table ASC, reason ASC
-- Active parts in cluster
SELECT
    database,
    table,
    partition,
    sum(rows) AS rows,
    count() AS part_count
FROM system.parts
WHERE (active = 1) AND (table LIKE '%') AND (database LIKE '%')
GROUP BY
    database,
    table,
    partition
ORDER BY part_count DESC
limit 20
```

### Attach and detach part or portition to table
```sql
alter table <DB>.<TABLE> attach part      'b0deaa35da556bc0c_1824_1824_0';
alter table <DB>.<TABLE> attach partition '876f020b03f4d84bb_646168_646168_0' | ALL ;
alter table <DB>.<TABLE> detach part      '7a16aac8d0949ac37_4264_4264_0';
alter table <DB>.<TABLE> detach partition '7a16aac4906b58c37';
```

### Part logs in Clickhouse
```sql
SELECT event_time, event_type, part_name, exception FROM system.part_log where part_name='7a16aac8d09bc37_2420_2420_0'
```

### Find expensive queries
```sql
SELECT
    type,
    event_time,
    initial_query_id,
    query_id,
    formatReadableSize(memory_usage) AS memory,
    ProfileEvents.Values[indexOf(ProfileEvents.Names, 'UserTimeMicroseconds')] AS userCPU,
    ProfileEvents.Values[indexOf(ProfileEvents.Names, 'SystemTimeMicroseconds')] AS systemCPU,
    normalizedQueryHash(query) AS normalized_query_hash
FROM system.query_log
ORDER BY memory_usage DESC
LIMIT 10;
-------------------------------
SELECT query
FROM system.query_log
WHERE initial_query_id = '43801b2a-7aeb-4cfc-8171-59a97d8bddf7'
-------------------------------
SELECT
    hostname(),
    type,
    event_time,
    initial_query_id,
    query_id,
    formatReadableSize(memory_usage) AS memory,
    ProfileEvents.Values[indexOf(ProfileEvents.Names, 'UserTimeMicroseconds')] AS userCPU,
    ProfileEvents.Values[indexOf(ProfileEvents.Names, 'SystemTimeMicroseconds')] AS systemCPU,
    normalizedQueryHash(query) AS normalized_query_hash
FROM cluster('{cluster}', system.query_log)
order by memory_usage desc limit 10;
```


### Debug queries
```sql
SELECT * FROM system.errors;
SELECT * FROM system.events LIMIT 5;
SELECT * FROM system.parts_columns LIMIT 1 FORMAT Vertical;
SELECT * FROM viewIfPermitted(SELECT message FROM system.warnings ELSE null('message String'))
SELECT query,query_start_time FROM system.query_log WHERE query_start_time > '2024-09-30 01:00:00' and query_start_time < '2024-09-30 23:59:00'
```

### New queries
```sql
ALTER TABLE your_table_name DROP PARTITION 'partition_id' WHERE condition;
SELECT uniq(*)/count(*) FROM invoice_items;

RELOAD ASYNCHRONOUS METRICS [ON CLUSTER cluster_name]
SYSTEM FLUSH LOGS [ON CLUSTER cluster_name]
SHUTDOWN
SYSTEM RELOAD USERS [ON CLUSTER cluster_name]

SYSTEM STOP DISTRIBUTED SENDS [db.]<distributed_table_name> [ON CLUSTER cluster_name]
SYSTEM FLUSH DISTRIBUTED [db.]<distributed_table_name> [ON CLUSTER cluster_name] [SETTINGS ...]

SYSTEM STOP  LISTEN [ON CLUSTER cluster_name] [QUERIES ALL | QUERIES DEFAULT | QUERIES CUSTOM | TCP | TCP WITH PROXY | TCP SECURE | HTTP | HTTPS | MYSQL | GRPC | POSTGRESQL | PROMETHEUS | CUSTOM 'protocol']
SYSTEM START LISTEN [ON CLUSTER cluster_name] [QUERIES ALL | QUERIES DEFAULT | QUERIES CUSTOM | TCP | TCP WITH PROXY | TCP SECURE | HTTP | HTTPS | MYSQL | GRPC | POSTGRESQL | PROMETHEUS | CUSTOM 'protocol']

SYSTEM REFRESH VIEW [db.]name

ALTER TABLE table_name [ON CLUSTER cluster] FREEZE [PARTITION partition_expr] [WITH NAME 'backup_name']

```

### Useful links

[altinity-kb-useful-queries](https://kb.altinity.com/altinity-kb-useful-queries/)

[operations/settings](https://clickhouse.com/docs/en/operations/settings/settings)