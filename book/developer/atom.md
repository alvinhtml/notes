# Atom

## 为什么选择 Atom
  - Web 技术
  - GitHub推动
  - 开源

## 基本的术语

  - 缓冲区
    - 缓冲区是 Atom 中一个文件的文本内容。在大多数描述中，它基本类似于文件，但是它是Atom在内存中存放的版本。例如你可以修改文本缓冲区的内容，但是如果你不保存文件，它就不会写到相关的文件中。

  - 面板（pane）
    - 面板是Atom中可见的部分。如果你去看我们刚才加载的欢迎界面，会看到四个面板 —— 标签栏（tab bar），行号栏（gutter，行号在里面），底部的状态栏（status bar），最后是文本编辑器。

## 命令面板（Palette）

如果在编辑器获得焦点时，按下 `cmd-shift-P`，就会弹出命令面板。

## 打开、修改和保存文件
### 打开文件
在Atom中打开文件有许多种方法，你可以在菜单栏选择“File >> Open”，或者按下cmd-O来从系统对话框中选择一个文件。


### 使用 atom 命令打开目录
```bash
atom ./ ../alvin-project/react-miniui
```

你也可以使用 `cmd-\` 或者命令面板的 `tree-view:toggle` 命令来隐藏和显示它。以及 `ctrl-0` 来在它上面设置焦点。当树视图具有焦点时，你可以按下 `a`、`m`、`d` 来添加、修改和删除文件和文件夹。你可以在树视图中简单地右键点击文件和文件夹，来查看许多不同的选项，包括在你的本地文件系统中展示文件，或者复制文件路径到你的剪贴板。

### 在项目中打开文件
如果你按下 `cmd-T` 或者 `cmd-P`，模糊查找工具的对话框就会弹出。这样能够让你通过输入路径的一部分，在项目中的任何目录中寻找任何文件。


### 编辑和保存文件

- cmd-s 保存文件
- cmd-shift-s 保存到一个不同的文件名
- cmd-alt-s 保存你在编辑器中打开的所有文件

## 在 Atom 中移动

Atom 自带许多 Emacs 的快捷键来浏览文档。要想上移或者下移一个字符，你可以按 `ctrl-P` 和 `ctrl-N`。左移或右移一个字符，按 `ctrl-B` 和 `ctrl-F`。这样等同于按下方向键，但是一些人不喜欢把他们的手移到方向键的位置。

除了单个字符的移动，还有一些其他的用于移动的快捷键。

- `alt-B`, `alt-left` 移动到单词开头。
- `alt-F`, `alt-right` 移动到单词末尾。
- `cmd-right`, `ctrl-E` 移动到整行末尾。
- `cmd-left`, `ctrl-A` 移动到整行开头。
- `cmd-up` 移动到文件开头。
- `cmd-down` 移动到文件末尾。

你也可以使用 `ctrl-G`，通过指定行和列 `55:16` 来直你可以通过cmd-t进行模糊查找。使用cmd-shift-r来查找存在于整个项目中的符号。接移动光标。这会弹出一个对话框问你想要跳到哪一行。

### 通过符号浏览

要想跳到一个方法声明之类的符号，按下 `cmd-r`。你也可以通过 `cmd-t` 进行模糊查找。使用 `cmd-shift-r` 来查找存在于整个项目中的符号。

你也可以使用 `ctrl-alt-down` 来跳到光标下的方法或者函数声明。

### Atom 书签

如果你按下 `cmd-F2`，Atom会给那一行加上书签。
按下 `F2` 之后，Atom 会跳到当前文件的下一个书签的位置。如果你按下 `shift-F2` 则会跳到上一个。
通过按下 `ctrl-F2`，你可以看到当前项目中的所有书签的列表，并可以快速筛选它们，跳到任何一个书签的位置。

## 文本选择
选择命令借鉴了很多查找命令。他们实际上具有相同的快捷键，只不过加了个shift。
- `ctrl-shift-P` 选择当前位置到上一行的相同列
- `ctrl-shift-N` 选择当前位置到下一行的相同列
- `ctrl-shift-B` 选择前一个字符
- `ctrl-shift-F` 选择后一个字符
- `alt-shift-B`, `alt-shift-left` 选择当前位置到单词开头
- `alt-shift-F`, `alt-shift-right` 选择当前位置到单词末尾
- `ctrl-shift-E`, `cmd-shift-right` 选择当前位置到整行末尾
- `ctrl-shift-A`, `cmd-shift-left` 选择当前位置到整行开头
- `cmd-shift-up` 选择当前位置到文件开头
- `cmd-shift-down` 选择当前位置到文件末尾
- `cmd-L` 选择整行
- `ctrl-shift-W`,`双击` 选择当前单词

## 编辑和删除文本
有一些用于基本操作的很酷的快捷键，他们十分易用。这些操作包括整行移动文本，整行复制，以及改变大小写。
- `ctrl-T` 交换光标两边字符的位置
- `cmd-J` 将下一行拼接到当前行的末尾
- `ctrl-cmd-up`, `ctrl-cmd-down` 上移或者下移当前行
- `cmd-shift-D` 复制当前行
- `cmd-K` & `cmd-U` 将当前字符转为大写
- `cmd-K` & `cmd-L` 将当前字符转为小写
- `cmd-alt-Q` 对当前选中区域格式化重排，使其一行的长度不超过80个字符（或者editor.preferredLineLength设置为什么都可以）

### 删除和剪切文本
- `ctrl-shift-K` 删除当前一行
- `cmd-delete` 删除当前位置到整行末尾的内容（在mac中为cmd-fn-backspace）
- `ctrl-K` 剪切当前位置到整行末尾的内容
- `cmd-backspace` 删除当前位置到整行开头的内容
- `alt-backspace`, `alt-H` 删除当前位置到单词开头的内容
- `alt-delete`, `alt-D` 删除当前位置到单词末尾的内容

### 多光标选择
- `cmd-click` 添加新的光标
- `cmd-shift-L` 将一个多重选择变为多个光标
- `ctrl-shift-up`, `ctrl-shift-down` 在当前光标之上或之下添加新的光标
- `cmd-D` 选择文档中与当前所选的单词相同的下一个单词
- `ctrl-cmd-G` 选择文档中与当前所选的单词相同的所有单词

### 括号

- `ctrl-m` 跳到光标下的括号所匹配的括号。如果没有，就跳到最近的后括号。
- `ctrl-cmd-m` 选择当前括号中所有文本
- `alt-cmd-.` 闭合当前的xml或html标签。

### 编码
- `ctrl-shift-U` 拉下菜单来修改文件编码

## 查找和替换
- `cmd-F` 在缓冲区中查找
- `cmd-shift-f` 在整个项目中查找

## 创建你自己的代码段
在你 ~/.atom 目录下的 snippets.cson 文件，存放了你的所有自定义的代码段，他们会在 Atom 运行时加载。你也可以通过 Atom > Snippets 菜单打开这个文件。

```bash
'.source.js':
  'console':
    'prefix': 'con'
    'body': 'console.log("$1", $2);'
```

对于长一些的模板，你可以使用"""来使用多行语法。

```bash
'.source.js':
  'if, else if, else':
    'prefix': 'ieie'
    'body': """
      if (${1:true}) {
        $2
      } else if (${3:false}) {
        $4
      } else {
        $5
      }
    """
```

## 折叠

你可以使用快捷键 `alt-cmd-[` 和 `alt-cmd-]` 来折叠和展开代码段。

使用 `alt-cmd-shift-{` 来折叠所有代码段，使用 `alt-cmd-shift-}` 来展开所有代码段。你也可以使用 `cmd-k`&`cmd-N` 来指定折叠的缩进级别，其中N是缩进深度。

最后，你可以折叠你代码或文本的任意一部分，通过按下 `ctrl-alt-cmd-F`，或者在命令面板中选择 “Fold Selection”。

## 面板

你可以通过使用 `cmd-k` & `arrow` 快捷键来横向或者纵向分割编辑器的面板，其中“arrow”是要分割的方向。面板被分割后，你可以使用 `cmd-k` & `cmd-arrow`快捷键在它们之间移动焦点，其中“arrow”是焦点要移动的方向。

## 语法
你可以按下 `ctrl-shift-L` 下拉语法选择工具。
