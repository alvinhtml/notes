# Homebrew

## brew services

```bash
# 显示服务状态
brew services list

# start|stop|restart 服务
brew services start mysql
```

## brew search node

```bash
brew search node
```

## brew info <名称>

使用 `brew info <名称>` 命令你可以看到当前可以切换的版本。


## brew ls --versions

```bash
# 查看所有 node 版本
brew ls --versions | grep node
```

## brew unlink <版本key>

```bash
brew unlink node
```

## 切换应用版本



```bash
# 查看当前安装的版本及位置
ls -l `which node`

# 查看通过 brew 安装的所有 node 版本
ls /usr/local/Cellar/node*

# 切换版本到 node@12
brew unlink node && brew link --overwrite --force node@12

#
echo 'export PATH="/usr/local/opt/node@12/bin:$PATH"' >> ~/.zshrc
```
