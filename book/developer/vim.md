# vim

## 移动光标

快捷键|描述
:-:|-
h, ←|光标向左移动一个字符
j, ↓|光标向下移动一个字符
k, ↑|光标向上移动一个字符
l, →|光标向右移动一个字符
30j, 30↓|向下移动 30 行
[Ctrl] + [f]|屏幕『向下』移动一页，相当于 [Page Down]
[Ctrl] + [b]|屏幕『向上』移动一页，相当于 [Page Up]
[Ctrl] + [d]|屏幕『向下』移动半页
[Ctrl] + [u]|屏幕『向上』移动半页
+|光标移动到非空格符的下一行
-|光标移动到非空格符的上一行
n<space>|20<space> 则光标会向后面移动 20 个字符距离。
H|光标移动到这个屏幕的最上方那一行的第一个字符
M|光标移动到这个屏幕的中央那一行的第一个字符
L|光标移动到这个屏幕的最下方那一行的第一个字符
G|移动到这个档案的最后一行(常用)
nG|n 为数字。移动到这个档案的第 n 行。例如 20G 则会移动到这个档案的第 20 行(可配合 :set nu)
gg|移动到这个档案的第一行，相当于 1G 啊！ (常用)
n<Enter>|n 为数字。光标向下移动 n 行(常用)

## 查找与替换

### 查找

在normal模式下按下 `/` 即可进入查找模式，输入要查找的字符串并按下回车。 Vim 会跳转到第一个匹配。按下 n 查找下一个，按下 N 查找上一个。

Vim 查找支持正则表达式，例如 `/vim$` 匹配行尾的 "vim"。 需要查找特殊字符需要转义，例如 `/vim\$` 匹配 "vim$"。

注意查找回车应当用`\n`，而替换为回车应当用`\r`（相当于<CR>）。


### 大小写敏感查找

在查找模式中加入`\c`表示大小写不敏感查找，`\C`表示大小写敏感查找。例如：

```
/foo\c
```

将会查找所有的"foo","FOO","Foo"等字符串。

大小写敏感配置

Vim 默认采用大小写敏感的查找，为了方便我们常常将其配置为大小写不敏感：

```
" 设置默认进行大小写不敏感查找
set ignorecase
" 如果有一个大写字母，则切换到大小写敏感查找
set smartcase
```

将上述设置粘贴到你的 `~/.vimrc`，重新打开Vim即可生效。

### 查找当前单词

在normal模式下按下`*`即可查找光标所在单词（word）， 要求每次出现的前后为空白字符或标点符号。例如当前为foo， 可以匹配foo bar中的foo，但不可匹配foobar中的foo。 这在查找函数名、变量名时非常有用。

按下`g*`即可查找光标所在单词的字符序列，每次出现前后字符无要求。 即foo bar和foobar中的foo均可被匹配到。

其他设置

```
:set incsearch 可以在敲键的同时搜索，按下回车把移动光标移动到匹配的词； 按下 Esc 取消搜索。

:set wrapscan 用来设置到文件尾部后是否重新从文件头开始搜索。
```

### 查找与替换

`:s`（substitute）命令用来查找和替换字符串。语法如下：

```
:{作用范围}s/{目标}/{替换}/{替换标志}
```
例如:%s/foo/bar/g会在全局范围(%)查找foo并替换为bar，所有出现都会被替换（g）。

### 作用范围

作用范围分为当前行、全文、选区等等。

当前行：

```
:s/foo/bar/g
```

全文：

```
:%s/foo/bar/g
```

选区，在Visual模式下选择区域后输入:，Vim即可自动补全为 :'<,'>。

```
:'<,'>s/foo/bar/g
```

2-11行：

```
:5,12s/foo/bar/g
```

当前行.与接下来两行+2：

```
:.,+2s/foo/bar/g
```

### 替换标志

上文中命令结尾的g即是替换标志之一，表示全局global替换（即替换目标的所有出现）。 还有很多其他有用的替换标志：

空替换标志表示只替换从光标位置开始，目标的第一次出现：

```
:%s/foo/bar
```

i表示大小写不敏感查找，I表示大小写敏感：

```bash
:%s/foo/bar/i
# 等效于模式中的\c（不敏感）或\C（敏感）
:%s/foo\c/bar
```

c表示需要确认，例如全局查找"foo"替换为"bar"并且需要确认：

```
:%s/foo/bar/gc
```

回车后Vim会将光标移动到每一次"foo"出现的位置，并提示

replace with bar (y/n/a/q/l/^E/^Y)?

按下y表示替换，n表示不替换，a表示替换所有，q表示退出查找模式， l表示替换当前位置并退出。^E与^Y是光标移动快捷键，参考： Vim中如何快速进行光标移动。

### 高亮设置

#### 高亮颜色设置

如果你像我一样觉得高亮的颜色不太舒服，可以在 ~/.vimrc 中进行设置：

```
highlight Search ctermbg=yellow ctermfg=black
highlight IncSearch ctermbg=black ctermfg=yellow
highlight MatchParen cterm=underline ctermbg=NONE ctermfg=NONE
```

上述配置指定 Search 结果的前景色（foreground）为黑色，背景色（background）为灰色； 渐进搜索的前景色为黑色，背景色为黄色；光标处的字符加下划线。

更多的CTERM颜色可以查阅：http://vim.wikia.com/wiki/Xterm256_color_names_for_console_Vim

#### 禁用/启用高亮

有木有觉得每次查找替换后 Vim 仍然高亮着搜索结果？ 可以手动让它停止高亮，在normal模式下输入：

```
:nohighlight
" 等效于
:nohl
```

其实上述命令禁用了所有高亮，只禁用搜索高亮的命令是:set nohlsearch。 下次搜索时需要:set hlsearch再次启动搜索高亮。

#### 延时禁用

怎么能够让Vim查找/替换后一段时间自动取消高亮，发生查找时自动开启呢？

```
" 当光标一段时间保持不动了，就禁用高亮
autocmd cursorhold * set nohlsearch
" 当输入查找命令时，再启用高亮
noremap n :set hlsearch<cr>n
noremap N :set hlsearch<cr>N
noremap / :set hlsearch<cr>/
noremap ? :set hlsearch<cr>?
noremap * *:set hlsearch<cr>
```

将上述配置粘贴到 ~/.vimrc，重新打开vim即可生效。

#### 一键禁用

如果延时禁用搜索高亮仍然不够舒服，可以设置快捷键来一键禁用/开启搜索高亮：

```
noremap n :set hlsearch<cr>n
noremap N :set hlsearch<cr>N
noremap / :set hlsearch<cr>/
noremap ? :set hlsearch<cr>?
noremap * *:set hlsearch<cr>

nnoremap <c-h> :call DisableHighlight()<cr>
function! DisableHighlight()
    set nohlsearch
endfunc
```
希望关闭高亮时只需要按下 `Ctrl+H`，当发生下次搜索时又会自动启用。

## 复制、粘贴、剪切

### 选择文本

- v+光标移动 （按字符选择）高亮选中所要的文本，然后进行各种操作（比如，d表示删除）。
- V （按行选择）
- v+选中的内容+c 更改选中的文字

### 复制

- y 用v命令选中文本后，用y进行复制
- yy 复制当前行，然后用p进行粘贴
- 5yy 复制从当前行开始的5行
- y_ 等同于yy
- Y 等同于yy
- yw 复制当前单词
- y$ 从当前位置复制到行尾
- y0 从当前位置复制到行首
- y^ 从当前位置复制到第一个非空白字符
- yG 从当前行复制到文件结束
- y20G 从当前行复制到第20行
- y?bar 复制至上一个出现bar的位置

### 粘贴

- p(小写) 在光标位置之后粘贴
- P(大写) 在光标位置之前粘贴

### 剪切

v + 方向键(hjkl)选中内容 + d 剪切 + p粘贴
