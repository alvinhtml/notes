# Makefile

## Makefile 简介

Makefile 是和 make 命令一起配合使用的.

很多大型项目的编译都是通过 Makefile 来组织的, Makefile不仅可以用来编译项目, 还可以用来组织我们平时的一些日常操作.

Makefile 主要有5个部分：

1. 显示规则
2. 隐晦规则
3. 变量定义
4. 文件指示
5. 注释

Makefile基本格式如下:

```
target ... : prerequisites ...
    command
    ...
    ...
```

其中：

- target        - 目标文件, 可以是 Object File, 也可以是可执行文件
- prerequisites - 生成 target 所需要的文件或者目标
- command       - make需要执行的命令 (任意的shell命令), Makefile中的命令必须以 [tab] 开头
