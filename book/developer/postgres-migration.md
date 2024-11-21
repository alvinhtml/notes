# PostgreSQL 数据库迁移笔记

## 1. 在远程服务器上导出 `hf` 数据库

在远程服务器上，你需要使用 `pg_dump` 工具将 `hf` 数据库导出为一个 SQL 文件。

运行以下命令来导出数据库：

```bash
pg_dump -U <远程用户> -h <远程服务器IP或主机名> -d hf -F c -f /path/to/save/hf_backup.dump
```

解释：

- `<远程用户>` 是远程数据库的用户，例如 `postgres`。
- `<远程服务器IP或主机名>` 是远程服务器的 IP 地址或主机名。
- `-d hf` 指定要导出的数据库。
- `-F c` 表示导出为自定义格式（你也可以选择 `-F t` 进行 tar 压缩）。
- `-f` 指定输出文件路径，如 `/path/to/save/hf_backup.dump`。

你也可以使用文本格式的 SQL 导出，命令如下：

```bash
pg_dump -U <远程用户> -h <远程服务器IP或主机名> -d hf > /path/to/save/hf_backup.sql
pg_dump -U postgres -d hf > /path/to/save/hf_backup.sql
```

## 2. 将备份文件复制到本地电脑

如果你使用的是远程服务器，下一步需要将导出的备份文件传输到你本地的电脑。可以通过 `scp` 进行传输。

例如：

```bash
scp <远程用户>@<远程服务器IP或主机名>:/path/to/save/hf_backup.dump /local/path/hf_backup.dump
```

或者使用 `rsync`：

```bash
rsync -avz <远程用户>@<远程服务器IP或主机名>:/path/to/save/hf_backup.dump /local/path/hf_backup.dump
```

## 3. 在本地电脑上恢复 `hf` 数据库

确保你的本地 PostgreSQL 服务已经启动，并且你已经创建了一个名为 `hf` 的数据库。如果还没有创建，你可以通过以下命令创建数据库：

```bash
createdb -U postgres hf
```

接着使用 `pg_restore` 或 `psql` 将备份恢复到本地数据库。

### 如果使用 `pg_dump` 的自定义格式 (dump 文件)：

```bash
pg_restore -U postgres -d hf /local/path/hf_backup.dump
```

### 如果使用的是 SQL 文件：

```bash
psql -U postgres -d hf -f /local/path/hf_backup.sql
```

## 4. 验证导入是否成功

最后，连接到 `hf` 数据库并检查数据是否成功导入：

```bash
psql -U postgres -d hf
```

你可以查询一些表或运行 `\dt` 来列出数据库中的表，确认导入成功。
