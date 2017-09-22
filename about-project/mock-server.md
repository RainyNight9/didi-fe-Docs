# SeaMockServer漫谈

在前后端分离项目中，为了让前端开发不依赖后端API，而能让前后端约定接口后，并行的相互独立开发，MockServer运应而生，
它扮演着相当重要的角色。
![示意图](../images/ms1.png)

下面回顾一下大家常用的MockServer:

#### 回顾以往的技术方案
1. 通过静态服务器
这种方式，在文件夹下每个接口写一个data.json，然后在前端提供两套url，根据环境变量或者配置文件的一个flag进行
生产环境和开发环境等切换。
> 优点是简单粗暴，但是具有侵入型，前端需要编写一些环境配置相关的代码，而且返回的数据编写麻烦，并且是固定的，
不便于开发中暴露一些边界情况，此外，它也不会检查入参是否合理...在小项目中使用应该是没问题的，但是一旦API庞大之后，
维护就有点力不从心了。

2. 通过Fiddler/Charles等抓包工具
这种方式做线上调试还挺方便的，但mock api还是有点麻烦。缺点就不赘述了，现在一般很少正式的用这种方法当mock工具。
> 该工具调试／解决bug还是超级给力的

3. 其它第三方MockServer工具

- json-server
[github地址](https://github.com/typicode/json-server)
> 这是一个非常著名轻巧的基于node的mock工具，使用非常简单方便，将一个db.json作为数据库，data.json里面的每个对象都是一个entity，
都可以对其进行增删改查，其默认API为restful风格的。此外，还可以为它编写中间件（middleware）
例如为每个请求增加头:
```js
module.exports = (req, res, next) => {
  res.header('X-Hello', 'World')
  next()
}
```
`json-server`的优点也是它的缺点，现今企业中很多系统API并不是Restful风格的，而`json-server`对非Restful api的支持度并不太友好，
并且它不能模拟延时返回/错误返回（不过它可以通过写中间件或者自定义模块解决这个问题）。

- swagger
[官方地址](https://swagger.io/)
swagger是一个著名的API文档编辑工具，也可以充当mock工具的功能，但是对于一些复杂的场景，例如修改“请求头”，随机返回数据等显得有点力不从心。
![swagger截图](../images/ms2.png)

还有一下三个基于java的mock工具
- easy-mock
[官网地址](http://easymock.org/)


- wiremock
[官网地址](http://wiremock.org/)

- mockserver
[github地址](https://github.com/jamesdbloom/mockserver)

**不具有独立性**， 这几个工具都是基于`java`的强大的mock工具，但是对于非`java`的项目无法愉快的使用。

#### Feature
上面介绍的一些方法，或多或少的都有一定的局限性，都只能满足一部分的需求。笔者以为，一个优秀的MockServer需要具备以下一些特性:

- 自定义返回的json数据
- 返回模版文件／静态资源
- 代理转发请求
- 缓存数据
- 动态切换数据源，能够实现开发环境／预发环境／生产环境的动态切换
- 集成swagger等工具，方便与后端共同维护API
- 方便的生成假数据
- 接口入参校验
- 轻量级，非侵入式（不用修改任何项目代码，也不依赖环境变量）
- 易用的交互界面, （electron or nwjs or web）
- 模拟延时返回/错误返回
- 也可以作为调试工具，取代Fiddler和Charles等工具。

以上是现有的一些MockServer的回顾，现有的MockServer并不能找到一个非常非常好用的，且同时满足以上特性的，
接下来，笔者将会编写一个好用的MockServer，解决开发中经常遇到的一些痛点。
