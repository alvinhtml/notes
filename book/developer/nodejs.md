# NodeJS 学习笔记
Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。Node.js 的包管理器 npm，是全球最大的开源库生态系统。

- 单线程
- 非阻塞I/O
- 事件驱动

### 单线程
在JAVA、PHP等服务器端语言中，会为每一个客房端链接创建一个新的线程，第个线程约耗费大约2MB的内存。

好处：操作系统完全不在有线程创建、销毁的时间开销。

坏处：一个线程崩溃，整修服务都会崩溃。

### 非阻塞I/O
I/O阻塞了代码的执行，极大的降低了程序的执行效率。

当程序遇到I/O操作，会转而处理后面的代码，将处理I/O结果的处理代码放到回调函数中，当I/O执行完毕后，将以事件的形式通知I/O操作的线程。为了处理异步的I/O，线程必须有事件循环，不断的检查有没有未处理的事件，依次处理。

### 事件驱动
同一时刻只能处理一个事件回调函数，在处理一个事件上回调函数中途可以转而处理其它事件（比如有新一用户链接了），然后返回继续执行原事件的回调函数，这种处理机制称为“事件环”机制。

不管是新用户的请求，还是老用户的I/O操作，都以事件方式加入事件环，等待处理。

## NodeJS 热部署

```bash
npm install hotnode -D
```