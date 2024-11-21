# Postgres 初如化

要启动 PostgreSQL 16 并初始化数据库，你可以按照以下步骤操作：

1. 启动 PostgreSQL 服务
   在 macOS 上，使用 Homebrew 安装 PostgreSQL 后，你可以使用以下命令启动服务：

```sh
brew services start postgresql
```

如果不想将 PostgreSQL 设置为开机启动，你也可以手动启动：

```sh
pg_ctl -D /usr/local/var/postgres start
```

2. 初始化数据库目录
   你想要使用自定义的数据目录 /Users/alvin/database/postgres/hf，所以首先需要初始化这个目录。

运行以下命令来初始化数据库目录：

```sh
initdb -D /Users/alvin/database/postgres/hf
```

3. 启动 PostgreSQL 并指向自定义数据目录
   初始化完成后，使用以下命令启动 PostgreSQL 并指定你的数据目录：

```sh
pg_ctl -D /Users/alvin/database/postgres/hf start
```

4. 创建用户 postgres 和数据库 hf
   PostgreSQL 安装完成后通常会自动创建一个超级用户 postgres。你可以直接用这个用户来创建数据库：

切换到 psql 命令行工具：

```sh
psql postgres
```

如果你要创建一个新用户 postgres，可以用以下命令：

```sql
CREATE USER postgres WITH SUPERUSER PASSWORD '123456';
```

然后创建一个名为 hf 的数据库：

```sql
CREATE DATABASE hf;
```

5. 验证
   你可以使用以下命令连接到新创建的数据库 hf：

```sh
psql -d hf -U postgres
```

6. 停止 PostgreSQL
   如果需要停止 PostgreSQL 服务，可以使用：

```sh
brew services stop postgresql
```

或者：

```sh
pg_ctl -D /Users/alvin/database/postgres/hf stop
```
