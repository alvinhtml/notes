# DOM API

## firstChild & firstElementChild

对于元素间的空格，IE9及以前版本不会返回文本节点，而其他所有浏览器都会返回文本节点。这样导致了在使用childNodes和firstChild等属性时的行为不一致。

```
=>childNodes
=>firstChild
=>lastChild
=>nextSibling（）
=>previousSibling（）
```

为了弥补上述这一差异，而同时又保持DOM规范不变，Element Traversal规范新定义了一组属性。

```
=>childElementCount：返回子元素（不包括文本节点和注释）的个数
=>firstElementChild：指向第一个子元素；firstChild的元素版。
=>lastElementChild：指向最后    一个子元素；lastChild的元素版。
=>previousElementSibling：指向前一个同辈元素；previousSibling的元素版。
=>nextElementSibling：指向最后一个同辈元素；nextSibling的元素版。
```
