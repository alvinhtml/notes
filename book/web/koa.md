# Koa 学习笔记

Koa 是一个新的 web 框架，由 Express 幕后的原班人马打造， 致力于成为 web 应用和 API 开发领域中的一个更小、更富有表现力、更健壮的基石。 通过利用 async 函数，Koa 帮你丢弃回调函数，并有力地增强错误处理。 Koa 并没有捆绑任何中间件， 而是提供了一套优雅的方法，帮助您快速而愉快地编写服务端应用程序。


## koa vs. express


- Express是一个完整的nodejs应用框架，Koa致力于核心中间件功能。
- Express和Koa都包含中间件，但实现方法却截然不同。核心Koa模块只是中间件内核。而Express包含一个完整的应用程序框架，具有路由和模板等功能。
- Koa的模块化程度更高，功能选项是单独的模块，用户可以根椐需求自己选择。
- 核心 koa 模块只有大约2千行代码，占用空间非常小。
- Express较为庞大，内置了一整套中间件功能，对于大部分应用场合你可以省掉自己选择和组合模块的时间。
- Koa 充分利用了ES7新的语法， 利用async/await来消除回调（callback）陷阱。
- koa2与koa1的最大区别是koa2实现异步是通过async/awaite，koa1实现异步是通过generator/yield，而express实现异步是通过回调函数的方式。
- koa2与express 提供的API大致相同，express是大而全，内置了大多数的中间件，更让人省心，koa2不绑定任何的框架，干净简洁，小而精，更容易实现定制化，扩展性好。
- express是没有提供ctx来提供上下流服务，需要更多的手动处理，express本身是不支持洋葱模型的数据流入流出能力的，需要引入其他的插件。

## 洋葱模型

在koa中，一切的流程都是中间件，数据流向遵循洋葱模型，先入后出，是按照类似堆栈的方式组织和执行的。koa的数据流入流出，next()后，会进入下一个中间件并执行，然后从最后一个中间件反向执行。

```js
const Koa = require('koa')
const app = new Koa()

const mid1 = async (ctx, next) => {
    ctx.body =  '前：' + '1\n'
    await next()
    ctx.body =   ctx.body + '后：' + '1\n'
}

const mid2 = async (ctx, next) => {
    ctx.body =    ctx.body + '前：'+ '2\n'
    await next()
    ctx.body =    ctx.body + '后：'+ '2\n'
}

const mid3 = async (ctx, next) => {
    ctx.body =  ctx.body + '前：'+  '3\n'
    await next()
    ctx.body =   ctx.body + '后：'+ '3\n'
}

app.use(mid1)
app.use(mid2)
app.use(mid3)

app.listen(3000)
```

```
前：1
前：2
前：3
后：3
后：2
后：1
```

## 中间件

- koa-router 路由中间件
- koa-bodyparser   POST数据处理的中间件
- koa-strict   静态资源管理的中间件
- koa2 模板引擎(ejs)
- koa-views 页面渲染相关的中间件
- koa-session SESSION 管理的中间件
- promise-mysql  数据库相关中间件

### koa-bodyparser

express 中有造好的轮子：body-parse 处理post请求，而koa 也有 koa-bodyparser

```bash
npm install koa-bodyparser --save
```

使用

```js
const bodyParser = require('koa-bodyparser');
app.use(bodyParser())
```

在代码中使用后，直接可以用ctx.request.body进行获取POST请求参数，中间件自动给我们作了解析

```js
var Koa = require('koa');
var bodyParser = require('koa-bodyparser');

var app = new Koa();
app.use(bodyParser());

app.use(async ctx => {
  // the parsed body will store in ctx.request.body
  // if nothing was parsed, body will be an empty object {}
  ctx.body = ctx.request.body;
})
```

## koa-router

express 中有 Router() 方法对路由进行解析 ， koa 也有 koa-router

```bash
npm install koa-router --save
```

```js
const Koa = require('koa');
const Router = require('koa-router');

const app = new Koa();
const router = new Router();

app
    .use(router.routes())
    .use(router.allowedMethods());

router
    .get('/', function (ctx, next) {
        ctx.body = 'gershon you are the best';
    })
    .get('/todo', function (ctx, next) {
         ctx.body = 'todo page';
    });

app.listen(3000, () => {
    console.log('http://127.0.0.1:3000');
});
```

allowedMethods，顾名思义：就是当前接口运行的 method。 比如，一个提供数据的接口，就可以设置为GET， 当客户端发送POST请求时，就会直接返回失败。

#### 设置前缀

```js
const router = new Router({
      prefix:'/gershon'
})
```

#### 路由层级

```js
const Koa = require('koa');
const Router = require('koa-router');

const app = new Koa();
let home = new Router();
let page = new Router();

// home 路由
home.get('/gershon', async (ctx) => {
    ctx.body = 'HOME gershon page';
}).get('/todo', async (ctx) => {
    ctx.body = 'HOME todo page';
});

// page 路由
page.get('/gershon', async (ctx) => {
    ctx.body = 'PAGE gershon page';
}).get('/todo', async (ctx) => {
    ctx.body = 'PAGE todo page';
});

// 父级路由
let router = new Router();
router.use('/home', home.routes(), home.allowedMethods())
    .use('/page', page.routes(), page.allowedMethods())

// 加载路由中间件
app
    .use(router.routes())
    .use(router.allowedMethods());


app.listen(3000, () => {
    console.log('http://127.0.0.1:3000');
});
```

### cookie

```
ctx.cookies.get(name,[optins]):读取上下文请求中的cookie。
ctx.cookies.set(name,value,[options])：在上下文中写入cookie。
```

```js
const Koa  = require('koa');
const app = new Koa();

app.use(async(ctx)=>{
    if(ctx.url=== '/index'){
        ctx.cookies.set(
            'MyName','gershon',{
                domain:'127.0.0.1', // 写cookie所在的域名
                path:'/index',       // 写cookie所在的路径
                maxAge:1000*60*60*24,   // cookie有效时长
                expires:new Date('2018-12-31'), // cookie失效时间
                httpOnly:false,  // 是否只用于http请求中获取
                overwrite:false  // 是否允许重写
            }
        );
        ctx.body = 'cookie is ok';
    }else{
        if( ctx.cookies.get('MyName')){
            ctx.body = ctx.cookies.get('MyName');
        }else{
            ctx.body = 'Cookie is none';
        }
    }
});

app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
})
```

### koa-session

当浏览器访问服务器并发送第一次请求时，服务器端会创建一个session对象，生成一个类似于key,value的键值对， 然后将key(cookie)返回到浏览器(客户)端，浏览器下次再访问时，携带key(cookie)，找到对应的session(value)。 客户的信息都保存在session中

```bash
npm install koa-session --save
```

```js
const session = require('koa-session');

//设置值 ctx.session.username = "张三";
// 获取值 ctx.session.username
```

### koa-static

```bash
npm install --save koa-static
```

```js
const Koa = require('koa')
const path = require('path')
const static = require('koa-static')

const app = new Koa()

const staticPath = './static'

app.use(static(
  path.join( __dirname,  staticPath)
))

app.use( async ( ctx ) => {
  ctx.body = 'hello world'
})

app.listen(3000, () => {
  console.log('[demo] static-use-middleware is starting at port 3000')
})
```

## Node 项目绑定域名

使用 nginx 的反向代理
