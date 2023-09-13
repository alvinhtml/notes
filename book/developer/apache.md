# apache 配置

## sudo apachectl -k graceful

`sudo apachectl -k graceful` 命令用于重新加载 Apache2 的配置文件，而无需停止正在运行的 Apache2 服务。这样可以使新的配置生效，而不会中断当前正在处理的连接。

具体来说，`-k graceful` 参数告诉 Apache2 服务器以优雅的方式重新加载配置文件。这意味着 Apache2 会平滑地关闭旧的工作进程，并启动新的工作进程来处理新的配置。这样可以确保在重新加载配置期间不会中断正在进行的连接，从而避免用户体验中断。

通过使用 `sudo apachectl -k graceful` 命令，你可以在不中断服务的情况下更新 Apache2 的配置文件，以便在重新加载后生效。
