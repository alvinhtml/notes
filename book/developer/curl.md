# curl

## curl 用法示例

```
-X 指定请求方法
-x 指定HTTP请求的代理
-H 指定请求标头
-d 发送POST请求提交的数据，使用-d参数后，会自动将请求转为POST，HTTP请求会自动加上标头Content-Type : application/x-www-form-urlencoded，可省略-X POST
-v 显示http通信的整个过程
-u 设置服务器认证的用户名和密码

-i 显示Response头信息，并打印源码
-I 显示Response头信息，不打印源码

-s 不输出错误和进度信息
-S 指定只输出错误信息

-L 自动跳转，curl默认不跟随跳转
-k 跳过SSL检测

-o 文件名 保存
-O 将URL的最后部分当作文件名保存
```
