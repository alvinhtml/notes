# GitBook 安装和使用
GitBook 是一个基于 Node.js 的命令行工具，支持 Markdown 和 AsciiDoc 两种语法格式，可以输出 HTML、PDF、eBook 等格式的电子书。

## 安装
GitBook 是一个基于 Node.js 的命令行工具，需要先下载安装 Node.js，安装完成之后，使用 npm 来安装 GitBook。

```bash
npm install gitbook-cli -g
```

安装完之后，就会多了一个 gitbook 命令。


## 使用
新建一个文件夹 `manual` 来存放电子书，进入 `manual` 运行 `gitbook init`。

```bash
gitbook init
```

运行成功后，会在 `manual` 文件夹下生成两个 `.md` 文件。
> - README.md
> - SUMMARY.md

`README.md` 是说明文档，而 `SUMMARY.md` 其实就是书的章节目录。

## 预览
如果想预览这本书籍，可以运行 `gitbook serve` ，然后在浏览器中打开 `localhost:4000`。

```bash
gitbook serve
```

## 构建
当你的电子书写作完成后，你可以执行 `gitbook build` 命令构建书籍，默认将生成的静态网站输出到 `_book` 目录，实际上，这一步已经包含在 `gitbook serve` 里面。

```bash
gitbook build [书籍路径] [输出路径]
```

serve 命令也可以指定端口：

```bash
gitbook serve --port 4001
```

## 生成电子书
生成电子书需要先安装 `ebook-convert`，这个是生成电子书(epub，mobi，pdf)所必需的插件。

### 苹果 OS X 系统
下载 [Caliber](https://calibre-ebook.com/download) 应用程序应用程序。将 `calibre.app` 移动到您的应用程序文件夹，启动 `ebook-convert` 完成第一次启动配置，然后关闭。创建一个指向 `ebook-convert` 工具的软件链接:

```bash
sudo ln -s /Applications/calibre.app/Contents/MacOS/ebook-convert /usr/local/Cellar/node/7.8.0/bin
```

`/usr/local/Cellar/node/7.8.0/bin` 是我 GitBook 命令所在的目录，如果是其它目录，要确保这个目录已经加入环境变量中。设置成功后就可以在任何目录下执行目录执行 `ebook-convert` 命令。

```bash
ebook-convert --version
```

看到版本信息，说明 `ebook-convert` 安装成功，接下来就可以成生各种格式的电子书了。

### ebook-convert

```
ebook-convert input_file output_file [options]
```

当然如果你想了解所有可选项的详细说明，也可以前往 [Calibre 官方帮助页面](https://manual.calibre-ebook.com/generated/en/ebook-convert.html)浏览。

```bash
ebook-convert example.txt example.mobi -h
```

### 生成电子书示例

生成 PDF 格式的电子书：

```bash
gitbook pdf ./ ./bookname.pdf
```

生成 epub 格式的电子书：

```bash
gitbook epub ./ ./bookname.epub
```

生成 mobi 格式的电子书：

```bash
gitbook mobi ./ ./bookname.mobi
```

## GitBook配置

### title
设置书本的标题

```json
"title" : "Gitbook Use"
```

### author
作者的相关信息

```json
"author" : "zhangjikai"
```

### description
本书的简单描述

```json
"description" : "记录Gitbook的配置和一些插件的使用"
```

### language
Gitbook使用的语言, 版本2.6.4中可选的语言如下：

```
en, ar, bn, cs, de, en, es, fa, fi, fr, he, it, ja, ko, no, pl, pt, ro, ru, sv, uk, vi, zh-hans, zh-tw
```
配置使用简体中文

```
"language" : "zh-hans",
```
### gitbook
指定使用的gitbook版本

```json
"gitbook" : "3.2.2",
"gitbook" : ">=3.0.0"
```
### root
指定存放 GitBook 文件（除了 book.json）的根目录

```json
"root": "."
```
### links
在左侧导航栏添加链接信息

```json
"links" : {
    "sidebar" : {
        "Home" : "http://zhangjikai.com"
    }
}
```

### styles
自定义页面样式， 默认情况下各generator对应的css文件

```json
"styles": {
    "website": "styles/website.css",
    "ebook": "styles/ebook.css",
    "pdf": "styles/pdf.css",
    "mobi": "styles/mobi.css",
    "epub": "styles/epub.css"
}
```
例如使`<h1> <h2>`标签有下边框， 可以在`website.css`中设置

```css
h1 , h2{
    border-bottom: 1px solid #EFEAEA;
}

```
### plugins
配置使用的插件

```json
"plugins": [
    "disqus"
]
```
添加新插件之后需要运行`gitbook install`来安装新的插件  

Gitbook默认带有5个插件：
* highlight
* search
* sharing
* font-settings
* livereload

如果要去除自带的插件， 可以在插件名称前面加 `-`

```json
"plugins": [
    "-search"
]
```
### pluginsConfig
配置插件的属性

```json
"pluginsConfig": {
    "fontsettings": {
        "theme": "sepia",
        "family": "serif",
        "size":  1
    }
}
```

## pandoc


```bash
brew install pandoc

# -f gfm github风格代码格式
pandoc -f gfm -t docx ./api.md -o api2.docx
```

## 参考

- [http://gitbook.wiliam.me/](http://gitbook.wiliam.me/)
