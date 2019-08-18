# SSH Key

## ssh 远程登陆



我们一般使用 ssh 远程登陆到另一台主机，例如使用 `ssh root@192.168.1.2` 远程登陆到 `192.168.1.2` ，如果使用密码登陆，每次都要输入密码，这样即麻烦，又不安全。如果想免密码登陆，就需要用到 ssh key, ssh key 可以让两台机器之间建立互信，从其中一台登陆到另一台时不需要密码。

假设现在有 A、B 两台主机，希望在 A 主机上使用 ssh 登陆到 B 主机，所需步骤如下：

```
1. 在 A 主机生成 ssh key 公钥（id_rsa.pub）和私钥（id_rsa）；
2. 将 A 主机的公钥（id_rsa.pub）添加到 B 主机的 ~/ssh/authorized_keys 文件。
```


## 检查 ssh key 是否存在

```sh
ls -al ~/.ssh
```

如果有文件 `id_rsa.pub` 或 `id_dsa.pub`， 说明 ssh key 已经存在。


## 生成新的 ssh key

```sh
ssh-keygen -t rsa -C "your_email@example.com"
```

接着执行 `ls -al ~/.ssh` 就可以看到生成的密钥对。


## 使用 ssh key

A 主机要使用 ssh key 登陆 B主机（192.168.1.2）, 需要将 A 主机的公钥 `id_rsa.pub` 添加到 B 主机 `authorized_keys` 文件。有两种办法，一种是 使用 `scp` 命令:

```sh
scp ~/.ssh/id_rsa.pub root@192.168.1.2:/root/.ssh/authorized_keys
```

另一种办法是复制 `id_rsa.pub` 的内容，并添加到 B 主机 `/root/.ssh/authorized_keys` 目录下下的 `authorized_keys` 文件。

```sh
cd ~/.ssh/
vim id_rsa.pub
cat id_rsa.pub >> authorized_keys
```
