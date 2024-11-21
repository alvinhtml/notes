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

4. 新增一条记录

```sql
INSERT INTO term_status (termid, online, warn, owner, ttype, ostype, regstat)
VALUES (808, true, false, NULL, NULL, NULL, NULL);
```

5. 随机更新一个终端的在线状态（online），并插入到 term_status 中

```sql
DO $$
DECLARE
  random_term_id INTEGER;
  new_online_status BOOLEAN;
BEGIN
  -- 从 term 表中随机选择一个终端 id
  SELECT id INTO random_term_id
  FROM term
  ORDER BY random()
  LIMIT 1;

  -- 随机生成新的在线状态
  new_online_status := (random() > 0.5);

  -- 更新 term 表中该终端的在线状态
  UPDATE term
  SET online = new_online_status
  WHERE id = random_term_id;

  -- 将更新后的状态插入到 term_status 表中
  INSERT INTO term_status (termid, online, warn, owner, ttype, ostype, regstat)
  SELECT id, online, warn, owner, ttype, ostype, regstat
  FROM term
  WHERE id = random_term_id;
END $$;
```

## 时间序列数据库查询

1. 2024-06-11 23:53

```sql
SELECT DATE_TRUNC('minute', time) AS minute,
       COUNT(*) AS online_term
FROM term_status
WHERE time >= '2024-06-11 23:28:11' AND time < '2024-06-11 23:54:11'
  AND online = TRUE
GROUP BY minute
ORDER BY minute;
```

## PostgreSQL 函数

### generate_series()

1. 数值序列

```sql
generate_series(start, stop, step)
```

- start：序列的起始值;
- stop：序列的终止值（包括在内）;
- step：步长（可选，默认为 1）。

2. 日期/时间序列

```sql
generate_series(start, stop, step_interval)
```

- start：序列的起始日期/时间;
- stop：序列的终止日期/时间（包括在内）;
- step_interval：步长，必须是一个 interval 类型。

```sql
SELECT * from generate_series(
	'2021-01-01',
    '2021-01-02', INTERVAL '1 hour'
  );

    generate_series
------------------------
 2021-01-01 00:00:00+00
 2021-01-01 01:00:00+00
 2021-01-01 02:00:00+00
 2021-01-01 03:00:00+00
 2021-01-01 04:00:00+00
 2021-01-01 05:00:00+00
 2021-01-01 06:00:00+00
 2021-01-01 07:00:00+00
 2021-01-01 08:00:00+00
 2021-01-01 09:00:00+00
 2021-01-01 10:00:00+00
 2021-01-01 11:00:00+00
 2021-01-01 12:00:00+00
 2021-01-01 13:00:00+00
 2021-01-01 14:00:00+00
 2021-01-01 15:00:00+00
 2021-01-01 16:00:00+00
 2021-01-01 17:00:00+00
 2021-01-01 18:00:00+00
 2021-01-01 19:00:00+00
 2021-01-01 20:00:00+00
 2021-01-01 21:00:00+00
 2021-01-01 22:00:00+00
 2021-01-01 23:00:00+00
 2021-01-02 00:00:00+00
(25 rows)
```

3. 插入数据

```sql
SELECT random()*100 as CPU, * FROM generate_series(1,5);

        cpu         | generate_series
--------------------+-----------------
 48.905450626783775 |               1
  71.94031820213382 |               2
 25.210553719011486 |               3
  19.24163308357194 |               4
  8.434915599133674 |               5
(5 rows)


-- generate_series() 这是一个 Set Return 函数，它返回一个数据的“表”（一个集合），就像我们从表中选择它一样。因此，就像我们使用 SQL 从常规表中选择数据一样，我们可以使用其他函数或静态值添加更多数据列。

SELECT * from generate_series(1,10) a, generate_series(1,2) b;

a |b|
--+-+
 1|1|
 2|1|
 3|1|
 4|1|
 5|1|
 6|1|
 7|1|
 8|1|
 9|1|
10|1|
 1|2|
 2|2|
 3|2|
 4|2|
 5|2|
 6|2|
 7|2|
 8|2|
 9|2|
10|2|
-- 10 * 2

-- 每隔一小时生成 12 个时间戳，一个表示 CPU 使用率的随机值，然后是第二个包含四个值的集合，表示虚假设备的 ID。这应该生成 48 行（例如，12 个时间戳 x 4 个设备 ID = 48 行）

SELECT time, device_id, random()*100 as cpu_usage
FROM generate_series(
	'2021-01-01 00:00:00',
    '2021-01-01 11:00:00',
    INTERVAL '1 hour'
  ) as time,
generate_series(1,4) device_id;


time               |device_id|cpu_usage          |
-------------------+---------+-------------------+
2021-01-01 00:00:00|        1|0.35415126479989567|
2021-01-01 01:00:00|        1| 14.013393572770028|
2021-01-01 02:00:00|        1|   88.5015939122006|
2021-01-01 03:00:00|        1|  97.49037810105996|
2021-01-01 04:00:00|        1|  50.22781125586846|
2021-01-01 05:00:00|        1|  77.93431470586931|
2021-01-01 06:00:00|        1|  45.73481750582076|
2021-01-01 07:00:00|        1|   70.7999843735724|
2021-01-01 08:00:00|        1|   4.72949831884506|
2021-01-01 09:00:00|        1|  85.29122113229981|
2021-01-01 10:00:00|        1| 14.539664281598874|
2021-01-01 11:00:00|        1|  45.95244258556228|
2021-01-01 00:00:00|        2|  46.41196423062297|
2021-01-01 01:00:00|        2|  74.39903569177027|
2021-01-01 02:00:00|        2|  85.44087332221935|
2021-01-01 03:00:00|        2|  4.329394730750735|
2021-01-01 04:00:00|        2| 54.645873866589056|
2021-01-01 05:00:00|        2|  6.544334492894777|
2021-01-01 06:00:00|        2|  39.05071228953645|
2021-01-01 07:00:00|        2|  71.07264365438404|
2021-01-01 08:00:00|        2|   72.4732704336219|
2021-01-01 09:00:00|        2| 34.533280927542975|
2021-01-01 10:00:00|        2| 26.764760864598003|
2021-01-01 11:00:00|        2|  62.32048879645227|
2021-01-01 00:00:00|        3|  63.01888063314749|
2021-01-01 01:00:00|        3|  21.70606884856987|
2021-01-01 02:00:00|        3|  32.47610779097485|
2021-01-01 03:00:00|        3| 47.565982341726354|
2021-01-01 04:00:00|        3|  64.34867263419619|
2021-01-01 05:00:00|        3|  57.74424991855476|
2021-01-01 06:00:00|        3| 55.593286571750156|
2021-01-01 07:00:00|        3|  36.92650110894995|
2021-01-01 08:00:00|        3| 53.166926049881624|
2021-01-01 09:00:00|        3| 10.009505806123897|
2021-01-01 10:00:00|        3| 58.067700285561585|
2021-01-01 11:00:00|        3|  81.58883725078034|
2021-01-01 00:00:00|        4|   78.1768041898232|
2021-01-01 01:00:00|        4|  84.51505102850199|
2021-01-01 02:00:00|        4| 24.029611792753514|
2021-01-01 03:00:00|        4|  17.08996115345549|
2021-01-01 04:00:00|        4| 29.642690955760997|
2021-01-01 05:00:00|        4|  90.83844806413275|
2021-01-01 06:00:00|        4| 6.5019080489854275|
2021-01-01 07:00:00|        4|    32.336484070672|
2021-01-01 08:00:00|        4|    55.595524107963|
2021-01-01 09:00:00|        4|   97.5442141375293|
2021-01-01 10:00:00|        4|   37.0741925805568|
2021-01-01 11:00:00|        4| 19.093927249791776|
```

4. 选择日期范围

```sql
-- 从当前时间往前推，以 1 小时的间隔，创建 6 个月

SELECT time, device_id, random()*100 as cpu_usage
FROM generate_series(
	now() - INTERVAL '6 months',
  now(),
    INTERVAL '1 hour'
  ) as time,
generate_series(1,4) device_id;
```

### time_bucket

`time_bucket` 是 TimescaleDB 提供的一个非常有用的函数，用于处理和分析时间序列数据。它将时间戳数据按指定的时间间隔进行聚合，从而简化了时间序列数据的处理。

语法
sql
复制代码
time_bucket(bucket_width, timestamp, [origin])
bucket_width：时间间隔，表示每个时间桶的宽度，例如 '1 minute', '1 hour', '1 day' 等。
timestamp：时间戳列，用于进行分桶。
origin（可选）：起始时间，默认是 '2000-01-03 00:00:00+00'。可以自定义以改变时间桶的对齐方式。

## 测试用

```sql


-- 创建 term 表
CREATE TABLE IF NOT EXISTS term_test (
id SERIAL PRIMARY KEY,
name VARCHAR(256),
mac VARCHAR(256),
ip VARCHAR(256),
online BOOLEAN,
warn BOOLEAN,
owner TEXT,
ttype TEXT,
ostype TEXT,
regstat INT
);

-- 插入 10 万条设备数据，同时在 term_status 中插入相应的数据
DO $$
DECLARE
  i INTEGER;
  mac_address TEXT;
  ip_address TEXT;
  new_term_id INTEGER;
BEGIN
  FOR i IN 1..10 LOOP
    -- 生成唯一的 MAC 地址
    mac_address := LPAD(TO_HEX((i / 65536)::INTEGER % 256), 2, '0') || ':' ||
      LPAD(TO_HEX((i / 256)::INTEGER % 256), 2, '0') || ':' ||
      LPAD(TO_HEX(i % 256), 2, '0') || ':' ||
      LPAD(TO_HEX((i / 16777216)::INTEGER % 256), 2, '0') || ':' ||
      LPAD(TO_HEX((i / 1048576)::INTEGER % 256), 2, '0') || ':' ||
      LPAD(TO_HEX((i / 4096)::INTEGER % 256), 2, '0');
    -- 生成唯一的 IP 地址
    ip_address := '192.168.' || ((i / 256)::INTEGER % 256) || '.' || (i % 256);

    -- 插入 term 表并获取新插入行的 id
    INSERT INTO term_test (name, mac, ip, online, warn, owner, ttype, ostype, regstat)
    VALUES (
        'Term' || i,
        mac_address,
        ip_address,
        (i % 2)::BOOLEAN,
        (i % 3)::BOOLEAN,
        NULL,
        'Type' || (i % 10),
        'OS' || (i % 5),
        (i % 6)
    )
    RETURNING id INTO new_term_id;

    -- 插入 term_status 表
    INSERT INTO term_status_test (termid, online, warn, owner, ttype, ostype, regstat)
    VALUES (
        new_term_id,
        (i % 2)::BOOLEAN,
        (i % 3)::BOOLEAN,
        NULL,
        'Type' || (i % 10),
        'OS' || (i % 5),
        (i % 6)
    );
  END LOOP;
END $$;



INSERT INTO term_status_test (termid, online, warn, owner, ttype, ostype, regstat)
VALUES (808, true, false, NULL, NULL, NULL, NULL);



WITH minute_intervals AS (
  SELECT generate_series(
    '2024-06-12 04:23:00'::timestamptz,
    '2024-06-12 05:07:00'::timestamptz,
    '2 minute'::interval
  ) AS minute
),
status_snapshots AS (
  SELECT
    mi.minute,
    ts.termid,
    ts.online,
    ts.time,
    row_number() OVER (PARTITION BY mi.minute, ts.termid ORDER BY ts.time DESC) AS rn
  FROM
    minute_intervals mi
  LEFT JOIN
    term_status_test ts ON ts.time <= mi.minute
)
SELECT
  minute,
  COUNT(DISTINCT termid) FILTER (WHERE online = true) AS online_count
FROM
  status_snapshots
WHERE
  rn = 1
GROUP BY
  minute
ORDER BY
  minute;
```

```sh
#!/bin/bash

# 数据库连接信息
PGHOST="localhost"
PGPORT="5432"
PGUSER="postgres"
PGDATABASE="tsdb"
PGPASSWORD="123456"

export PGPASSWORD=$PGPASSWORD

# 随机选择 1 到 3 个 termid 并插入到 status 表中
SQL="
DO \$\$
DECLARE
    r RECORD;
    current_online INTEGER;
    new_online INTEGER;
BEGIN
    FOR r IN
        SELECT id
        FROM term
        ORDER BY random()
        LIMIT floor(random() * 3 + 1)
    LOOP
        -- 查询当前 termid 的最新 online 状态
        SELECT online INTO current_online
        FROM status
        WHERE termid = r.id
        ORDER BY time DESC
        LIMIT 1;

        -- 如果没有找到记录，则将 online 设置为随机值
        IF NOT FOUND THEN
            new_online := CASE WHEN random() < 0.5 THEN 0 ELSE 1 END;
        ELSE
            -- 取反 online 状态
            new_online := CASE WHEN current_online = 0 THEN 1 ELSE 0 END;
        END IF;

        -- 插入新记录，将 boolean 转换为 integer
        INSERT INTO status (termid, time, online, warn, owner, ttype, ostype, regstat)
        VALUES (
            r.id,
            now(),
            new_online,
            CASE WHEN random() < 0.5 THEN 1 ELSE 0 END,
            NULL,
            'Type' || (FLOOR(random() * 10)::INT),
            'OS' || (FLOOR(random() * 5)::INT),
            NULL
        );
    END LOOP;
END \$\$;
"

# 循环执行 SQL 语句
while true
do
    psql -h $PGHOST -p $PGPORT -U $PGUSER -d $PGDATABASE -c "$SQL"
    sleep 1
done
```

## 查询示例

1. 获取每个整十分钟在线的终端总数

```sql
-- 获取每个整点数，在线的终端的数量
WITH intervals AS (
    SELECT
        generate_series(
            date_trunc('hour', '2024-06-15 00:00:00'::timestamp),
            date_trunc('hour', '2024-06-16 00:00:00'::timestamp),
            '1 hour'
        ) AS interval
)
SELECT
    i.interval,
    COUNT(DISTINCT s.termid) AS online_count
FROM
    intervals i
LEFT JOIN (
    SELECT
        time,
        termid,
        online,
        lead(time) OVER (PARTITION BY termid ORDER BY time) AS next_time
    FROM
        status_m
    WHERE
        time BETWEEN '2024-06-15 00:00:00' AND '2024-06-16 00:00:00'
) s
ON
    s.online = 1
    AND i.interval >= s.time
    AND i.interval < COALESCE(s.next_time, '2024-06-16 00:00:01')
GROUP BY
    i.interval
ORDER BY
    i.interval;


WITH changes AS (
    SELECT
        time,
        termid,
        online,
        lead(time) OVER (PARTITION BY termid ORDER BY time) AS next_time
    FROM
        status_m
    WHERE
        time BETWEEN '2024-06-15 00:00:00' AND '2024-06-16 00:00:00'
),
expanded AS (
    SELECT
        time AS start_time,
        COALESCE(next_time, '2024-06-17 00:00:00') AS end_time,
        termid,
        online
    FROM
        changes
    WHERE
        online = 1
),
intervals AS (
    SELECT
        generate_series(
            '2024-06-15 00:00:00'::timestamp,
            '2024-06-16 00:00:00'::timestamp,
            '1 hour'
        ) AS interval
)
SELECT
    i.interval,
    COUNT(DISTINCT e.termid) AS online_count
FROM
    intervals i
LEFT JOIN
    expanded e
ON
    i.interval >= e.start_time
    AND i.interval < e.end_time
GROUP BY
    i.interval
ORDER BY
    i.interval;



-- 验证
WITH changes AS (
    SELECT
        termid,
        online,
        time,
        lead(time) OVER (PARTITION BY termid ORDER BY time) AS next_time
    FROM
        status_m
    WHERE
        time < '2024-06-14 20:20:00'
),
current_status AS (
    SELECT
        termid,
        online
    FROM
        changes
    WHERE
        online = 1
        AND (next_time IS NULL OR next_time > '2024-06-14 20:20:00')
)
SELECT COUNT(DISTINCT termid) AS online_count
FROM current_status;
```

```sql


CREATE VIEW online_status_view AS
WITH changes AS (
    SELECT
        time,
        termid,
        online,
        lead(time) OVER (PARTITION BY termid ORDER BY time) AS next_time
    FROM
        status
    WHERE
        time BETWEEN '2024-06-14 05:59:11' AND '2024-06-14 05:59:22'
),
expanded AS (
    SELECT
        time AS start_time,
        COALESCE(next_time, '2024-06-14 05:59:23') AS end_time, -- 修改这里，确保覆盖到最后一秒
        termid,
        online
    FROM
        changes
),
seconds AS (
    SELECT
        generate_series(
            '2024-06-14 05:59:11'::timestamp,
            '2024-06-14 05:59:22'::timestamp,
            '1 second'
        ) AS second
)
SELECT
    s.second,
    COUNT(DISTINCT e.termid) AS online_count,
    ARRAY_AGG(DISTINCT e.termid) AS online_ids
FROM
    seconds s
LEFT JOIN
    expanded e
ON
    s.second >= e.start_time
    AND s.second < e.end_time
    AND e.online = 1
GROUP BY
    s.second
ORDER BY
    s.second;

------------------

SELECT
    os.online_ids,
    t.id AS term_id,
    t.online,
    t.warn,
    s.time AS status_time,
    s.owner,
    s.ttype,
    s.ostype,
    s.regstat
FROM
    online_status_view os
JOIN
    term t ON t.id = ANY(os.online_ids)
JOIN LATERAL (
    SELECT *
    FROM status
    WHERE termid = t.id
    ORDER BY time DESC
    LIMIT 1
) s ON true
WHERE
    os.second = '2024-06-14 05:59:18';

-------------------------

SELECT
    os.online_ids,
    t.id AS term_id,
    s.online,
    s.warn,
    s.time AS status_time,
    s.owner,
    s.ttype,
    s.ostype,
    s.regstat
FROM
    online_status_view os
JOIN
    term t ON t.id = ANY(os.online_ids)
JOIN LATERAL (
    SELECT *
    FROM status
    WHERE termid = t.id
      AND time <= '2024-06-14 05:59:18'
    ORDER BY time DESC
    LIMIT 1
) s ON true
WHERE
    os.second = '2024-06-14 05:59:18';
```

```sql
-- term 表示终端
-- 创建表
CREATE TABLE status_m (
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
SELECT create_hypertable('status_m', 'time');

-- 为 status 表创建基于 time 和 termid 的复合索引
CREATE INDEX idx_status_time_termid ON status_m (time, termid);

-- 创建 term（终端） 表
CREATE TABLE IF NOT EXISTS term_m (
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
-- 为 term 表创建 10 W条测试数据，要求IP和MAC不能重复



DO $$
DECLARE
  i INTEGER;
  mac_address TEXT;
  ip_address TEXT;
BEGIN
  FOR i IN 1..10 LOOP
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
        (i % 2),
        (i % 2),
        'Owner ' || i,
        'Type ' || (i % 10),
        'OS ' || (i % 5),
        (i % 6)
    );
  END LOOP;
END $$;


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
    INSERT INTO term_m (name, mac, ip, online, warn, owner, ttype, ostype, regstat)
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

## 参考

- [Get started with Timescale](https://docs.timescale.com/getting-started/latest/)
- [timescale alternatives](https://www.timescale.com/learn/timescale-alternatives)
