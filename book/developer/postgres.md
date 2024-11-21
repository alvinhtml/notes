# PostgreSQL

## PostgreSQL 命令行操作

1. 建立数据库连接

```sh
# psql -h IP地址 -p 端口 -U 数据库名
psql -U postgre hf
```

2. 访问数据库

- 1、列举数据库：`\l`
- 2、选择数据库：`\c` 数据库名
- 3、查看该某个库中的所有表：`\dt`
- 4、显示某个表的字段信息：`\d table_name`
- 5、切换数据库：`\c` interface
- 6、查看某个库中的某个表结构：`\d` 表名
- 7、查看某个库中某个表的记录：`select * from apps limit 1;`
- 8、显示字符集：`\encoding`
- 9、退出 psgl：`\q`
- 10、切换表显示方向 `\x`

## 常用 SQL

### 删除表

```sql
DROP TABLE table_name;

-- 如果你不确定表是否存在，并且想在表不存在时避免错误，可以使用 IF EXISTS 子句：
DROP TABLE IF EXISTS table_name;
```

## Postgres 索引

Postgres 现在支持 多种类型 的索引，了解基础知识是使用 Postgres 的关键部分。

数据库索引的作用类似于书后的索引部分。数据库索引存储有关数据行在表中的位置的信息，因此数据库不必扫描整个表以获取信息。当数据库有要检索的查询时，它首先转到索引，然后使用该信息检索请求的数据。

索引有自己独立的数据结构，它们是 Postgres 数据定义语言 (DDL) 的一部分。它们与数据表和其他对象一起存储在磁盘上。

- **B-tree** 索引是最常见的索引类型，如果您创建索引但不指定类型，它将是默认索引。B 树索引非常适合对您经常查询的信息进行通用索引。
- **BRIN** 索引是块范围索引，专门针对您正在搜索的数据以块为单位的非常大的数据集，例如时间戳和日期范围。众所周知，它们非常高效且节省空间。
- **GIST** 索引在您的数据库中构建搜索树，最常用于空间数据库和全文搜索用例。
- 当您在单个列中有多个值时， **GIN** 索引很有用，这在您存储数组或 json 数据时很常见。

## B 树索引

对于 B-Tree 示例，我使用了一些开放的天气数据，其中包含按类型、损坏、时间和位置 1 的数据事件。对于一个非常基本的索引，我将找到冬季风暴的所有内容。添加此索引后，这意味着要获取此数据，数据库不必扫描所有天气事件来获取有关恶劣天气事件的其他数据，它已经知道在哪里查找这些数据。

开始查询

```sql
SELECT *
**FROM** weather
**where** event_type='Winter Storm'
```

指标解释前分析

```sql
Seq Scan on weather  (cost=0.00..9204.64 rows=3158 width=853) (actual time=0.008..27.619 rows=3182 loops=1)
Execution Time: 27.778 ms
```

指数

```sql
CREATE INDEX idx_weather_type ON weather(event_type);
```

指标解释后分析

```sql
Bitmap Index Scan on idx_weather_type  (cost=0.00..35.98 rows=3158 width=0) (actual time=0.247..0.247 rows=3182 loops=1)
Execution Time: 3.005 ms
```

### 多列 B 树索引

索引并不总是只为单列创建—​​—Postgres 还支持多列索引。如果您知道一次要在多个列上进行大量查询，这些可能会很有用。

开始查询

```sql
SELECT *
FROM weather
WHERE event_type='Winter Storm'
  AND damage_crops > '0'
```

指标解释前分析

```sql
Seq Scan on weather  (cost=0.00..9402.36 rows=2586 width=853) (actual time=0.007..67.365 rows=2896 loops=1)
Execution Time: 67.499 ms
```

严重性和类型的多列索引

```sql
CREATE INDEX idx_storm_crop ON weather(event_type,damage_crops);
```

索引解释后分析

```sql
Bitmap Index Scan on idx_storm_crop  (cost=0.00..38.15 rows=2586 width=0) (actual time=0.339..0.339 rows=2896 loops=1)
Execution Time: 2.204 ms
```

如果您忘记了所有创建索引并需要查看所拥有的内容，这将显示特定表上的所有索引：

```sql
SELECT indexname, indexdef
FROM pg_indexes
WHERE tablename = 'weatherevents';
```

`Drop index indexname` 如果您想再次测试，将删除它。

## BRIN 索引

BRIN 通常在使用大型数据集时非常有用，特别是在使用时间序列或时间戳数据的情况下。为此，我使用了来自 IoT 数据集 2 的样本，每天有数千行数据。

开始查询

```sql
SELECT device, humidity
FROM iot
WHERE ts between '2020-07-13 0:00' AND '2020-07-14 0:00'
```

先解释再分析

```sql
Parallel Seq Scan on iot  (cost=0.00..10363.95 rows=896 width=28) (actual time=12.710..42.080 rows=16707 loops=3)
Execution Time: 67.851 ms
```

添加索引

```sql
CREATE INDEX iot_time ON iot USING brin(ts);
```

解释后分析

```sql
Bitmap Index Scan on iot_time  (cost=0.00..12.26 rows=54025 width=0) (actual time=0.046..0.047 rows=10240 loops=1)
Execution Time: 10.513 ms
```

您经常会听到 BRIN 索引非常节省空间。因此，当您处理索引时，您可能想要查询实际索引的大小。例如：

```sql
pg_size_pretty(pg_relation_size('iot_time'));
```

## 使用 GIST 的基本空间索引

如果您在数据库中使用空间数据，您可能有很多数据，索引可能是至关重要的。对于空间索引，我使用了 PostGIS 教程 3 中的数据和示例。如果您刚开始使用空间数据，我强烈推荐本教程。

SQL 查询

```sql
SELECT count(blocks.blkid)
FROM nyc_census_blocks blocks
JOIN nyc_subway_stations subways
ON ST_Contains(blocks.geom, subways.geom)
 WHERE subways.name LIKE 'B%';
```

开始解释分析

```sql
Timing: Generation 4.364 ms, Inlining 360.628 ms, Optimization 615.663 ms, Emission 559.573 ms, Total 1540.227 ms
Execution Time: 1467.916 ms
```

空间 GIST 索引示例

```sql
CREATE INDEX nyc_census_blocks_geom_idx
  ON nyc_census_blocks
  USING GIST (geom);
```

索引后解释分析

```sql
Execution Time: 7.575 ms
```

空间索引可以产生巨大的影响。

## JSON 的 GIN 索引

JSON 数据已被 Postgres 用户广泛采用，并且 Postgres 核心项目已经接受了 具有广泛特性的 JSON 数据类型的采用。如果您的数据在每个字段中列出了多个对象，那么有些索引类型会非常有用。GIN 索引类型通常用于此目的。对于这个例子，我使用了一个来自 NASA 的 json 文件，其中包含流星位置信息 4。

SQL 查询

```sql
SELECT data -> 'name' as name
FROM meteors
WHERE data @> '{"mass": "100"}';
```

解释索引前分析

```sql
Parallel Seq Scan on meteors  (cost=0.00..23926.28 rows=4245 width=32) (actual time=0.065..114.114 rows=1024 loops=3)
Execution Time: 123.698 ms
```

指数

```sql
CREATE INDEX gin_test ON meteors USING gin(data)
```

解释索引后分析

```sql
Bitmap Index Scan on gin_test  (cost=0.00..116.40 rows=10187 width=0) (actual time=12.164..12.164 rows=3072 loops=1)
Execution Time: 22.017 ms
```
