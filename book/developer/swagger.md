# Swagger

Swagger 是一个规范且完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。

Swagger 的目标是对 REST API 定义一个标准且和语言无关的接口，可以让人和计算机拥有无须访问源码、文档或网络流量监测就可以发现和理解服务的能力。当通过 Swagger 进行正确定义，用户可以理解远程服务并使用最少实现逻辑与远程服务进行交互。与为底层编程所实现的接口类似，Swagger 消除了调用服务时可能会有的猜测。

## Swagger 的优势

支持 API 自动生成同步的在线文档：使用 Swagger 后可以直接通过代码生成文档，不再需要自己手动编写接口文档了，对程序员来说非常方便，可以节约写文档的时间去学习新技术。
提供 Web 页面在线测试 API：光有文档还不够，Swagger 生成的文档还支持在线测试。参数和格式都定好了，直接在界面上输入参数对应的值即可在线测试接口。

## Swagger 使用的注解及其说明

```
@Api：用在类上，说明该类的作用。

@ApiOperation：注解来给API增加方法说明。

@ApiImplicitParams : 用在方法上包含一组参数说明。

@ApiImplicitParam：用来注解来给方法入参增加说明。
参数：
   ·paramType：指定参数放在哪个地方
      ··header：请求参数放置于Request Header，使用@RequestHeader获取
      ··query：请求参数放置于请求地址，使用@RequestParam获取
      ··path：（用于restful接口）-->请求参数的获取：@PathVariable
      ··body：（不常用）
      ··form（不常用）
   ·name：参数名
   ·dataType：参数类型
   ·required：参数是否必须传(true | false)
   ·value：说明参数的意思
   ·defaultValue：参数的默认值

@ApiResponses：用于表示一组响应

@ApiResponse：用在@ApiResponses中，一般用于表达一个错误的响应信息
      ——code：数字，例如400
      ——message：信息，例如"请求参数异常!"
      ——response：抛出异常的类   

@ApiModel：描述一个Model的信息（一般用在请求参数无法使用@ApiImplicitParam注解进行描述的时候）

@ApiModelProperty：描述一个model的属性
```
