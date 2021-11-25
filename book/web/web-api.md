# Web API




## Blob

`Blob` 对象表示一个不可变、原始数据的类文件对象。它的数据可以按文本或二进制的格式进行读取，也可以转换成 [ReadableStream](https://developer.mozilla.org/zh-CN/docs/Web/API/ReadableStream) 来用于数据操作。

`Blob` 表示的不一定是JavaScript原生格式的数据。File 接口基于Blob，继承了 blob 的功能并将其扩展使其支持用户系统上的文件。

要从其他非blob对象和数据构造一个 `Blob，请使用` `Blob()` 构造函数。要创建一个 blob 数据的子集 blob，请使用 `slice()` 方法。要获取用户文件系统上的文件对应的 `Blob` 对象，请参阅 File 文档。

```
Blob(blobParts[, options])
```

返回一个新创建的 Blob 对象，其内容由参数中给定的数组串联组成。

### 属性

- `Blob.size` 只读，Blob 对象中所包含数据的大小（字节）。
- `Blob.type` 只读，一个字符串，表明该

### 方法

- `Blob.slice([start[, end[, contentType]]])` 返回一个新的 Blob 对象，包含了源 Blob 对象中指定范围内的数据。
- `Blob.stream()` 返回一个能读取blob内容的 ReadableStream。
- `Blob.text()` 返回一个promise且包含blob所有内容的UTF-8格式的 USVString。
- `Blob.arrayBuffer()` 返回一个promise且包含blob所有内容的二进制格式的 ArrayBuffer


## File

文件（File）接口提供有关文件的信息，并允许网页中的 JavaScript 访问其内容。

通常情况下， File 对象是来自用户在一个 `<input>` 元素上选择文件后返回的 `FileList` 对象,也可以是来自由拖放操作生成的 `DataTransfer` 对象，或者来自 `HTMLCanvasElement` 上的 `mozGetAsFile()` API。在Gecko中，特权代码可以创建代表任何本地文件的File对象，而无需用户交互（有关详细信息，请参阅注意事项。)

File 对象是特殊类型的 Blob，且可以用在任意的 Blob 类型的 context 中。比如说， FileReader, URL.createObjectURL(), createImageBitmap(), 及 XMLHttpRequest.send() 都能处理 Blob 和 File。


### 属性

`File` 接口也继承了 `Blob` 接口的属性：

- `File.lastModified` 只读

返回当前 File 对象所引用文件最后修改时间，自 UNIX 时间起始值（1970年1月1日 00:00:00 UTC）以来的毫秒数。

- `File.lastModifiedDate` 只读

返回当前 File 对象所引用文件最后修改时间的 Date 对象。

- `File.name` 只读

返回当前 File 对象所引用文件的名字。

- `File.size` 只读

返回文件的大小。

- `File.webkitRelativePath` 只读

返回 File 相关的 path 或 URL。

- `File.type` 只读

返回文件的 多用途互联网邮件扩展类型（MIME Type）

### 方法

File 接口没有定义任何方法，但是它从 Blob 接口继承了以下方法：

- `Blob.slice([start[, end[, contentType]]])`

返回一个新的 Blob 对象，它包含有源 Blob 对象中指定范围内的数据。


## MIME 类型

媒体类型（通常称为 Multipurpose Internet Mail Extensions 或 MIME 类型 ）是一种标准，用来表示文档、文件或字节流的性质和格式。它在IETF RFC 6838中进行了定义和标准化。

### 语法

通用结构

```
type/subtype
```

MIME的组成结构非常简单；由类型与子类型两个字符串中间用'/'分隔而组成。不允许空格存在。type 表示可以被分多个子类的独立类别。subtype 表示细分后的每个类型。

MIME类型对大小写不敏感，但是传统写法都是小写。

[类型参考](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types)
