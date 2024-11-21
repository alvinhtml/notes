# Postgre TimescaleDB

TimescaleDB 是一个建立在 PostgreSQL 之上的时间序列数据库。它旨在处理大量带有时间戳的数据，并为时间序列数据提供高效且可扩展的查询性能。

TimescaleDB 与常规 PostgreSQL 的主要区别在于，TimescaleDB 针对存储和查询时间序列数据进行了优化，而 PostgreSQL 是一个通用数据库，可以处理各种类型的数据。TimescaleDB 扩展了 PostgreSQL，以提供专门用于时间序列数据管理的其他特性和功能，例如自动时间分区、优化索引和压缩。

TimescaleDB 采用分布式超表架构，根据时间间隔对数据进行分区，实现对大量数据随时间推移的高效查询。它还为时间序列数据提供高级分析和可视化功能，包括连续聚合和窗口函数。

## TimescaleDB 的主要功能和优势：

1. 可扩展性：TimescaleDB 设计为水平扩展，这意味着它可以处理大量数据和高流量负载。

2. 时间分区：TimescaleDB 自动按时间对数据进行分区，即数据根据时间间隔存储在单独的分区中。这样可以更高效地查询和更快地分析时间序列数据。

3. 高级索引：TimescaleDB 提供高级索引功能，可以快速高效地查询时间序列数据。TimescaleDB 使用多维索引来优化同时涉及时间维度和其他维度的查询。

4. 压缩：TimescaleDB 内置了时间序列数据压缩功能，降低了存储需求，提高了查询性能。TimescaleDB 使用运行长度编码、增量编码和位打包的组合来实现高级别的压缩。

5. 连续聚合：TimescaleDB 提供连续聚合函数，支持对时间序列数据进行实时分析。这使用户能够实时查看趋势和模式，这对于主动决策非常有用。

6. 窗口函数：TimescaleDB 提供了窗口函数，可以对时间序列数据进行更高级的分析。窗口函数允许用户计算滑动时间窗口内的聚合，这对于识别时间序列数据中的趋势和模式非常有用。

## 安装和设置

在安装 TimescaleDB 之前，需要先安装 PostgreSQL。然后运行以下命令来安装扩展：

```sh
sudo apt install timescaledb-2-postgresql-13
```

安装 TimescaleDB 扩展后，通过运行以下命令创建 TimescaleDB 数据库：

```sh
CREATE DATABASE tsdb;
```

创建 TimescaleDB 数据库后，通过运行以下命令来启用 TimescaleDB 扩展：

```sh
psql -d tsdb -c "CREATE EXTENSION IF NOT EXISTS timescaledb"
```

## 时间序列数据建模

1. 以终端状态为例，创建一个时间序列表

```sql
-- 创建表
CREATE TABLE status (
time TIMESTAMPTZ NOT NULL DEFAULT now(),
termid INTEGER NOT NULL,
online INT,
warn INT,
owner TEXT,
ttype TEXT,
ostype TEXT,
regstat INT,
PRIMARY KEY (termid, time)
);

-- 转换为 TimescaleDB 的 hypertable
SELECT create_hypertable('status', 'time');

-- 为 status 表创建基于 time 和 termid 的复合索引
CREATE INDEX idx_status_time_termid ON status (time, termid);
```

2. 创建终端表

```sql
-- 创建 term 表
CREATE TABLE IF NOT EXISTS term (
id SERIAL PRIMARY KEY,
name VARCHAR(256),
mac VARCHAR(32),
ip VARCHAR(32),
online INT,
warn INT,
owner TEXT,
ttype TEXT,
ostype TEXT,
regstat INT
);
```

3. 插入 10 万条示例数据

```sql
-- 插入 10 万条设备数据，同时在 term_status 中插入相应的数据
DO $$
DECLARE
  i INTEGER;
  mac_address TEXT;
  ip_address TEXT;
BEGIN
  FOR i IN 1..100000 LOOP
    -- Generate a unique MAC address
    mac_address := LPAD(TO_HEX((i / 65536) % 256), 2, '0') || ':' ||
                   LPAD(TO_HEX((i / 256) % 256), 2, '0') || ':' ||
                   LPAD(TO_HEX(i % 256), 2, '0') || ':' ||
                   LPAD(TO_HEX((i / 16777216) % 256), 2, '0') || ':' ||
                   LPAD(TO_HEX((i / 1048576) % 256), 2, '0') || ':' ||
                   LPAD(TO_HEX((i / 4096) % 256), 2, '0');

    -- Generate a unique IP address
    ip_address := '192.168.' || ((i / 256) % 256) || '.' || (i % 256);

    -- Insert into term table
    INSERT INTO term (name, mac, ip, online, warn, owner, ttype, ostype, regstat)
    VALUES (
        'Term ' || i,
        mac_address,
        ip_address,
        (random() * 2)::INTEGER,
        (random() * 2)::INTEGER,
        'Owner ' || (random() * 1000)::INTEGER,
        'Type ' || (random() * 10)::INTEGER,
        'OS ' || (random() * 5)::INTEGER,
        (random() * 6)::INTEGER
    );
  END LOOP;
END $$;
```

## 查询示例

1. 获取每个整十分钟在线的终端总数

```sql
CREATE MATERIALIZED VIEW status_m_intervals_mv AS
WITH status_m_intervals AS (
    SELECT
        termid,
        time AS start_time,
        lead(time) OVER (PARTITION BY termid ORDER BY time) AS end_time,
        online
    FROM status_m
)
SELECT
    termid,
    start_time,
    COALESCE(end_time, timestamp '2024-06-16 00:00:00') AS end_time,
    online
FROM
    status_m_intervals
WHERE
    start_time < timestamp '2024-06-16 00:00:00';


WITH hours AS (
    SELECT generate_series(
        timestamp '2024-06-15 00:00:00',
        timestamp '2024-06-16 00:00:00',
        interval '1 hour'
    ) AS hour
)
SELECT
    h.hour,
    count(DISTINCT e.termid) AS online_terms
FROM
    hours h
LEFT JOIN
    status_m_intervals_mv e
ON
    e.start_time <= h.hour
    AND e.end_time > h.hour
    AND e.online = 1
GROUP BY
    h.hour
ORDER BY
    h.hour;



WITH hours AS (
    SELECT generate_series(
        timestamp '2024-06-15 00:00:00',
        timestamp '2024-06-16 00:00:00',
        interval '1 hour'
    ) AS hour
),
status_m_intervals AS (
    SELECT
        t1.termid,
        t1.time AS start_time,
        lead(t1.time) OVER (PARTITION BY t1.termid ORDER BY t1.time) AS end_time,
        t1.online
    FROM status_m t1
    WHERE t1.time < timestamp '2024-06-16 00:00:00'
)
SELECT
    h.hour,
    count(DISTINCT t.termid) as online_terms
FROM
    hours h
LEFT JOIN
    status_m_intervals t
ON
    t.start_time <= h.hour
    AND (t.end_time > h.hour OR t.end_time IS NULL)
    AND t.online = 1
GROUP BY
    h.hour
ORDER BY
    h.hour;




WITH target_times AS (
    SELECT generate_series(
        timestamp '2024-06-15 00:00:00',
        timestamp '2024-06-16 00:00:00',
        interval '1 hour'
    ) AS target_time
)
SELECT
    t.target_time,
    COUNT(DISTINCT a.termid) AS online_terms
FROM
    target_times t
LEFT JOIN
    status_m_hourly_aggregates a
ON
    a.hour = t.target_time
WHERE
    a.online = true
GROUP BY
    t.target_time
ORDER BY
    t.target_time;



WITH target_times AS (
    SELECT generate_series(
        timestamp '2024-06-14 00:00:00',
        timestamp '2024-06-17 00:00:00',
        interval '1 hour'
    ) AS target_time
)
SELECT
    t.target_time,
    COUNT(DISTINCT a.termid) AS online_terms
FROM
    target_times t
LEFT JOIN
    status_m_hourly_aggregates a
ON
    a.hour = t.target_time
WHERE
    a.online = true
GROUP BY
    t.target_time
ORDER BY
    t.target_time;
```

2. 验证查询

```sql
WITH changes AS (
    SELECT
        termid,
        online,
        time,
        lead(time) OVER (PARTITION BY termid ORDER BY time) AS next_time
    FROM
        status_m
    WHERE
        time < '2024-06-15 20:00:00'
),
current_status AS (
    SELECT
        termid,
        online
    FROM
        changes
    WHERE
        online = 1
        AND (next_time IS NULL OR next_time > '2024-06-15 20:00:00')
)
SELECT COUNT(DISTINCT termid) AS online_count
FROM current_status;
```

3. 查询 2024-06-14 19:00 ~ 2024-06-15 04:00 每小时曾在线过的终端数量

```sql
WITH hourly_intervals AS (
    SELECT generate_series(
        '2024-06-14 19:00:00'::timestamp,
        '2024-06-15 04:00:00'::timestamp,
        '1 hour'
    ) AS interval_start
),
changes AS (
    SELECT
        termid,
        online,
        time,
        lead(time) OVER (PARTITION BY termid ORDER BY time) AS next_time
    FROM
        status_m
),
expanded AS (
    SELECT
        termid,
        time AS start_time,
        COALESCE(next_time, '2024-06-15 04:00:00') AS end_time
    FROM
        changes
    WHERE
        online = 1
),
online_counts AS (
    SELECT
        i.interval_start,
        COUNT(DISTINCT e.termid) AS online_count
    FROM
        hourly_intervals i
    LEFT JOIN
        expanded e
    ON
        i.interval_start < e.end_time
        AND (i.interval_start + interval '1 hour') > e.start_time
    GROUP BY
        i.interval_start
)
SELECT
    interval_start || ' - ' || (interval_start + interval '1 hour') AS interval_period,
    online_count
FROM
    online_counts
ORDER BY
    interval_start;
```

4. 验证

```sql
WITH changes AS (
    SELECT
        termid,
        online,
        time,
        lead(time) OVER (PARTITION BY termid ORDER BY time) AS next_time
    FROM
        status_m
),

expanded AS (
    SELECT
        termid,
        time AS start_time,
        COALESCE(next_time, '2024-06-15 03:00:00'::timestamp) AS end_time
    FROM
        changes
    WHERE
        online = 1
)

SELECT
    COUNT(DISTINCT e.termid) AS online_count
FROM
    expanded e
WHERE
    e.start_time < '2024-06-15 03:00:00'::timestamp
    AND e.end_time > '2024-06-15 02:00:00'::timestamp;
```

```sql
select * from status_m order by time asc limit 10;

             time              | termid | online | warn | owner | ttype | ostype | regstat
-------------------------------+--------+--------+------+-------+-------+--------+---------
 2024-06-14 19:50:33.267046+00 |  36661 |      1 |    0 |       | Type1 | OS1    |
 2024-06-14 19:50:33.267046+00 |  25193 |      1 |    1 |       | Type2 | OS4    |
 2024-06-14 19:50:33.267046+00 |  99010 |      1 |    1 |       | Type6 | OS0    |
 2024-06-14 19:50:38.374645+00 |  97589 |      1 |    0 |       | Type9 | OS2    |
 2024-06-14 19:50:38.374645+00 |   8074 |      1 |    1 |       | Type2 | OS3    |
 2024-06-14 19:50:38.374645+00 |  80686 |      1 |    0 |       | Type5 | OS4    |
 2024-06-14 19:50:38.374645+00 |  65755 |      1 |    0 |       | Type1 | OS2    |
 2024-06-14 19:50:43.44538+00  |  55714 |      1 |    1 |       | Type0 | OS1    |
 2024-06-14 19:50:43.44538+00  |  20894 |      1 |    1 |       | Type7 | OS4    |
 2024-06-14 19:50:43.44538+00  |  23346 |      0 |    0 |       | Type4 | OS0    |
 ...
 2024-06-17 19:34:57.398296+00 |  24179 |      0 |    1 |       | Type3 | OS4    |

我有一表 postgres timescaledb，里面有 700 万条数据，其中 online 只在 term 状态发生改变时记录一条数据，现在我想查询， 2024-06-15 00:00 ~ 2024-06-16 00:00  每个整点时刻在线的term数，如果一个term在这个时间之前上线了，并且一直没有下线，则视为在线，给出最快的查询 sql















使用连续聚合拿的数据和直接查询的数据不一致

tsdb=# WITH target_times AS (
tsdb(#     SELECT generate_series(
tsdb(#         timestamp '2024-06-15 00:00:00',
tsdb(#         timestamp '2024-06-16 00:00:00',
tsdb(#         interval '1 hour'
tsdb(#     ) AS target_time
tsdb(# )
tsdb-# SELECT
tsdb-#     t.target_time,
tsdb-#     COUNT(DISTINCT a.termid) AS online_terms
tsdb-# FROM
tsdb-#     target_times t
tsdb-# LEFT JOIN
tsdb-#     status_m_hourly_aggregates a
tsdb-# ON
tsdb-#     a.hour = t.target_time
tsdb-# WHERE
tsdb-#     a.online = true
tsdb-# GROUP BY
tsdb-#     t.target_time
tsdb-# ORDER BY
tsdb-#     t.target_time;
     target_time     | online_terms
---------------------+--------------
 2024-06-15 00:00:00 |        46410
 2024-06-15 01:00:00 |        47742
 2024-06-15 02:00:00 |        47153
 2024-06-15 03:00:00 |        46375
 2024-06-15 04:00:00 |        46585
 2024-06-15 05:00:00 |        47269
 2024-06-15 06:00:00 |        47287
 2024-06-15 07:00:00 |        47921
 2024-06-15 08:00:00 |        47154
 2024-06-15 09:00:00 |        46129
 2024-06-15 10:00:00 |        46673
 2024-06-15 11:00:00 |        45211
 2024-06-15 12:00:00 |        48033
 2024-06-15 13:00:00 |        45932
 2024-06-15 14:00:00 |        45792
 2024-06-15 15:00:00 |        44885
 2024-06-15 16:00:00 |        47596
 2024-06-15 17:00:00 |        45978
 2024-06-15 18:00:00 |        46562
 2024-06-15 19:00:00 |        47554
 2024-06-15 20:00:00 |        46479
 2024-06-15 21:00:00 |        46752
 2024-06-15 22:00:00 |        47236
 2024-06-15 23:00:00 |        47239
 2024-06-16 00:00:00 |        47980
(25 rows)



Time: 21010.137 ms (00:21.010)
tsdb=# WITH hours AS (
tsdb(#     SELECT generate_series(
tsdb(#         timestamp '2024-06-15 00:00:00',
tsdb(#         timestamp '2024-06-16 00:00:00',
tsdb(#         interval '1 hour'
tsdb(#     ) AS hour
tsdb(# ),
tsdb-# status_m_intervals AS (
tsdb(#     SELECT
tsdb(#         t1.termid,
tsdb(#         t1.time AS start_time,
tsdb(#         lead(t1.time) OVER (PARTITION BY t1.termid ORDER BY t1.time) AS end_time,
tsdb(#         t1.online
tsdb(#     FROM status_m t1
tsdb(#     WHERE t1.time < timestamp '2024-06-16 00:00:00'
tsdb(# )
tsdb-# SELECT
tsdb-#     h.hour,
tsdb-#     count(DISTINCT t.termid) as online_terms
tsdb-# FROM
tsdb-#     hours h
tsdb-# LEFT JOIN
tsdb-#     status_m_intervals t
tsdb-# ON
tsdb-#     t.start_time <= h.hour
tsdb-#     AND (t.end_time > h.hour OR t.end_time IS NULL)
tsdb-#     AND t.online = 1
tsdb-# GROUP BY
tsdb-#     h.hour
tsdb-# ORDER BY
tsdb-#     h.hour;
        hour         | online_terms
---------------------+--------------
 2024-06-15 00:00:00 |        45544
 2024-06-15 01:00:00 |        48477
 2024-06-15 02:00:00 |        49449
 2024-06-15 03:00:00 |        49833
 2024-06-15 04:00:00 |        50059
 2024-06-15 05:00:00 |        50103
 2024-06-15 06:00:00 |        50253
 2024-06-15 07:00:00 |        50179
 2024-06-15 08:00:00 |        50042
 2024-06-15 09:00:00 |        50038
 2024-06-15 10:00:00 |        50022
 2024-06-15 11:00:00 |        50203
 2024-06-15 12:00:00 |        49710
 2024-06-15 13:00:00 |        49799
 2024-06-15 14:00:00 |        50010
 2024-06-15 15:00:00 |        50188
 2024-06-15 16:00:00 |        49991
 2024-06-15 17:00:00 |        50208
 2024-06-15 18:00:00 |        49781
 2024-06-15 19:00:00 |        50242
 2024-06-15 20:00:00 |        50007
 2024-06-15 21:00:00 |        49761
 2024-06-15 22:00:00 |        49958
 2024-06-15 23:00:00 |        50112
 2024-06-16 00:00:00 |        50210
(25 rows)
```
