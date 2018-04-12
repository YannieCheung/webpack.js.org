---
title: 开发中 Server(DevServer)
sort: 9
contributors:
  - sokra
  - skipjack
  - spacek33z
  - charlespwd
  - orteth01
---

webpack-dev-server 能够用于快速开发应用程序。请查看[“如何开发？”](/guides/development)入门。

此页面描述影响 webpack-dev-server(简写为：dev-server) 行为的选项。

T> 与 [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware) 兼容的选项旁边有 🔑。


## `devServer`

`object`

通过来自 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) 的这些选项，能够用多种方式改变其行为。这里有一个简单的例子，所有来自 `dist/` 目录的文件都做 gzip 压缩和提供为服务：

```js
devServer: {
  contentBase: path.join(__dirname, "dist"),
  compress: true,
  port: 9000
}
```

当服务器启动时，在解析模块列表之前会有一条消息：

```bash
http://localhost:9000/
webpack output is served from /build/
Content not from webpack is served from /path/to/dist/
```

这将给出一些背景知识，就能知道服务器的访问位置，并且知道服务已启动。

如果你通过 Node.js API 来使用 dev-server， `devServer` 中的选项将被忽略。将选项作为第二个参数传入： `new WebpackDevServer(compiler, {...})`。关于如何通过 Node.js API 使用 webpack-dev-server 的示例，请[查看此处](https://github.com/webpack/webpack-dev-server/tree/master/examples/api/simple)。

W> 请注意，当[导出多个配置](/configuration/configuration-types/#exporting-multiple-configurations)时，第一个配置中的`devServer`选项被用于数组中的所有配置。

T> 如果你有麻烦，导航到路由`/webpack-dev-server`以显示服务器上的文件，举个栗子，`http://localhost:9000/webpack-dev-server`.


## `devServer.after`

`function`

在服务端所有中间件执行之后，提供执行自定义中间件的能力。

```js
after(app){
  // do fancy stuff
}
```

## `devServer.allowedHosts`

`array`

该选项允许你为那些可访问的开发服务端提供白名单

```js
allowedHosts: [
  'host.com',
  'subdomain.host.com',
  'subdomain2.host.com',
  'host2.com'
]
```

模拟Django的`ALLOWED_HOSTS`，以`.`开头的值将被当做一个子域名通配符，`.host.com`可以匹配`host.com`，`www.host.com`以及任何其他包含`host.com`子域名的域名。

```js
//这个配置与上一个例子是同样的效果，如果新的子域名需要访问
//服务器，好处就是无需更新你的配置
allowedHosts: [
    '.host.com',
    'host2.com'
]
```

在命令行中使用这个选项`--allowed-hosts`
```bash
webpack-dev-server --entry /entry/file --output-path /output/path --allowed-hosts .host.com,host2.com
```

## `devServer.before`

`function`

提供在服务端所有中间件执行之前执行自定义中间件的能力。这可以用来定义自定义函数。例如

```js
before(app){
  app.get('/some/path', function(req, res) {
    res.json({ custom: 'response' });
  });
}
```

## `devServer.bonjour`

该选项开启时，通过零配置网络广播服务端
（This option broadcasts the server via ZeroConf networking on start）

```js
bonjour: true
```

通过命令行使用

```bash
webpack-dev-server --bonjour
```


## `devServer.clientLogLevel`

`string`

当使用*内联模式(inline mode)*时，在开发工具(DevTools)的控制台(console)将显示消息，如：在重新加载之前，在一个错误之前，或者模块热替换(Hot Module Replacement)启用时。这可能显得很繁琐。

你可以阻止所有这些消息显示，使用这个选项：

```js
clientLogLevel: "none"
```

通过命令行使用

```bash
webpack-dev-server --client-log-level none
```

可能的值有 `none`, `error`, `warning` 或者 `info`（默认值）。


## `devServer.color` - 仅命令行

`boolean`

开启/关闭控制台的颜色

```bash
webpack-dev-server --color
```


## `devServer.compress`

`boolean`

一切服务都启用[gzip 压缩](https://betterexplained.com/articles/how-to-optimize-your-site-with-gzip-compression/)：

```js
compress: true
```

通过命令行使用

```bash
webpack-dev-server --compress
```


## `devServer.contentBase`

`boolean` `string` `array`

告诉服务器从哪里提供内容。只有在你想要提供静态文件时才需要。[`devServer.publicPath`](#devserver-publicpath-) 将用于确定应该从哪里提供 bundle，并且此选项优先。

默认情况下，将使用当前工作目录作为提供内容的目录，但是你可以修改为其他目录：

```js
contentBase: path.join(__dirname, "public")
```

注意，推荐使用绝对路径。

但是也可以从多个目录提供内容：

```js
contentBase: [path.join(__dirname, "public"), path.join(__dirname, "assets")]
```

禁用 `contentBase`：

```js
contentBase: false
```

通过命令行使用

```bash
webpack-dev-server --content-base /path/to/content/dir
```


## `devServer.disableHostCheck`

`boolean`

该选项设置为true时，将绕过主机检测，不推荐这么做，不检测主机容易受到DNS rebinding攻击。

```js
disableHostCheck: true
```

通过命令行使用

```bash
webpack-dev-server --disable-host-check
```


## `devServer.filename` 🔑

`string`

在**惰性模式**中，此选项可减少编译。
默认在**惰性模式**，每个请求结果都会产生全新的编译。使用 `filename`，可以只在某个文件被请求时编译。

如果 `output.filename` 设置为 `bundle.js` ，`filename` 使用如下：

```js
lazy: true,
filename: "bundle.js"
```

现在只有在请求 `/bundle.js` 时候，才会编译 bundle。

T> `filename` 在不使用**惰性加载**时没有效果。


## `devServer.headers` 🔑

`object`

在所有响应中添加首部内容：

```js
headers: {
  "X-Custom-Foo": "bar"
}
```


## `devServer.historyApiFallback`

`boolean` `object`

当使用 [HTML5 History API](https://developer.mozilla.org/en-US/docs/Web/API/History) 时，任意的 `404` 响应都可能需要被替代为 `index.html`。通过传入以下启用：

```js
historyApiFallback: true
```

通过传入一个对象，比如使用 `rewrites` 这个选项，此行为可进一步地控制：

```js
historyApiFallback: {
  rewrites: [
    { from: /^\/$/, to: '/views/landing.html' },
    { from: /^\/subpage/, to: '/views/subpage.html' },
    { from: /./, to: '/views/404.html' }
  ]
}
```

当路径中使用点(dot)（常见于 Angular），你可能需要使用 `disableDotRule`：

```js
historyApiFallback: {
  disableDotRule: true
}
```

通过命令行使用

```bash
webpack-dev-server --history-api-fallback
```

更多选项和信息，查看 [connect-history-api-fallback](https://github.com/bripkens/connect-history-api-fallback) 文档。


## `devServer.host`

`string`

指定使用一个 host。默认是 `localhost`。如果你希望服务器外部可访问，指定如下：

```js
host: "0.0.0.0"
```

通过命令行使用

```bash
webpack-dev-server --host 0.0.0.0
```


## `devServer.hot`

`boolean`

启用 webpack 的模块热替换特性：

```js
hot: true
```

T> 如果想完全使用HMR，`webpack.HotModuleReplacementPlugin`是必须的。如果`webpack`或`webpack-dev-server`靠`--hot`运行，这个插件将被自动的添加，所有你不需要在你的 `webpack.config.js`添加它。参见[HMR相关页面](/concepts/hot-module-replacement)获取更多信息。


## `devServer.hotOnly`

`boolean`

启用HMR（参见[`devServer.hot`](#devserver-hot)），但是在构建失败时不刷新页面。
Enables Hot Module Replacement (see [`devServer.hot`](#devserver-hot)) without page refresh as fallback in case of build failures.

```js
hotOnly: true
```

通过命令行使用

```bash
webpack-dev-server --hot-only
```


## `devServer.https`

`boolean` `object`

默认情况下，dev-server 通过 HTTP 提供服务。也可以选择带有 HTTPS 的 HTTP/2 提供服务：

```js
https: true
```

使用以下设置自签名证书，但是你可以提供自己的：

```js
https: {
  key: fs.readFileSync("/path/to/server.key"),
  cert: fs.readFileSync("/path/to/server.crt"),
  ca: fs.readFileSync("/path/to/ca.pem"),
}
```

此对象直接传递到 Node.js HTTPS 模块，所以更多信息请查看 [HTTPS 文档](https://nodejs.org/api/https.html)。

通过命令行使用

```bash
webpack-dev-server --https
```

在命令行中，使用以下选项传递你自己的签名证书。

```bash
webpack-dev-server --https --key /path/to/server.key --cert /path/to/server.crt --cacert /path/to/ca.pem
```

## `devServer.index`

`string`

index文件的文件名

```javascript
index: 'index.htm'
```


## `devServer.info` - 仅用于命令行

`boolean`

默认开启，在命令行显示信息

```bash
webpack-dev-server --info=false
```


## `devServer.inline`

`boolean`

在 dev-server 的两种不同模式之间切换。默认情况下，应用程序启用*内联模式(inline mode)*。这意味着一段处理实时重载的脚本被插入到你的包(bundle)中，并且构建消息将会出现在浏览器控制台。

也可以使用 **iframe 模式**，它在通知栏下面使用 `<iframe>` 标签，包含了关于构建的消息。切换到 **iframe 模式**：

```js
inline: false
```

通过命令行使用

```bash
webpack-dev-server --inline=false
```

T> 推荐使用模块热替换的内联模式，因为它包含来自 websocket 的 HMR 触发器。轮询模式可以作为替代方案，但需要一个额外的入口点：`'webpack/hot/poll?1000'`。


## `devServer.lazy` 🔑

`boolean`

当启用 `lazy` 时，dev-server 只有在请求时才编译包(bundle)。这意味着 webpack 不会监视任何文件改动。我们称之为“**惰性模式**”。

```js
lazy: true
```

通过命令行使用

```bash
webpack-dev-server --lazy
```

T> `watchOptions` 在使用**惰性模式**时无效。

T> 如果使用命令行工具(CLI)，请确保**内联模式(inline mode)**被禁用。


## `devServer.noInfo` 🔑

`boolean`

启用 `noInfo` 后，诸如「启动时和每次保存之后，那些显示的 webpack 包(bundle)信息」的消息将被隐藏。错误和警告仍然会显示。

```js
noInfo: true
```


## `devServer.open`

`boolean`

当启用时，服务端会自动打开浏览器

```js
open: true
```

通过命令行使用

```bash
webpack-dev-server --open
```

如果没有提供浏览器，打开默认浏览器。如果要指定一个不同的浏览器，只要把它名字传进去

```bash
webpack-dev-server --open 'Google Chrome'
```


## `devServer.openPage`

`string`

打开浏览器时导航到一个具体页面

```js
openPage: '/different/page'
```

在命令行使用

```bash
webpack-dev-server --open-page "/different/page"
```


## `devServer.overlay`

`boolean` `object`

Shows a full-screen overlay in the browser when there are compiler errors or warnings. Disabled by default. If you want to show only compiler errors:

```js
overlay: true
```

If you want to show warnings as well as errors:

```js
overlay: {
  warnings: true,
  errors: true
}
```


## `devServer.pfx`

`string`

When used via the CLI, a path to an SSL .pfx file. If used in options, it should be the bytestream of the .pfx file.

```js
pfx: '/path/to/file.pfx'
```

Usage via the CLI

```bash
webpack-dev-server --pfx /path/to/file.pfx
```


## `devServer.pfxPassphrase`

`string`

The passphrase to a SSL PFX file.

```js
pfxPassphrase: 'passphrase'
```

Usage via the CLI

```bash
webpack-dev-server --pfx-passphrase passphrase
```


## `devServer.port`

`number`

指定要监听请求的端口号：

```js
port: 8080
```

Usage via the CLI

```bash
webpack-dev-server --port 8080
```


## `devServer.proxy`

`object`

如果你有单独的后端开发服务器 API，并且希望在同域名下发送 API 请求 ，那么代理某些 URL 会很有用。

dev-server 使用了非常强大的 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware) 包。更多高级用法，请查阅其[文档](https://github.com/chimurai/http-proxy-middleware#options)。

在 `localhost:3000` 上有后端服务的话，你可以这样启用代理：

```js
proxy: {
  "/api": "http://localhost:3000"
}
```

请求到 `/api/users` 现在会被代理到请求 `http://localhost:3000/api/users`。

如果你不想始终传递 `/api` ，则需要重写路径：

```js
proxy: {
  "/api": {
    target: "http://localhost:3000",
    pathRewrite: {"^/api" : ""}
  }
}
```

默认情况下，不接受运行在 HTTPS 上，且使用了无效证书的后端服务器。如果你想要接受，修改配置如下：

```js
proxy: {
  "/api": {
    target: "https://other-server.example.com",
    secure: false
  }
}
```

有时你不想代理所有的请求。可以基于一个函数的返回值绕过代理。

在函数中你可以访问请求体、响应体和代理选项。必须返回 `false` 或路径，来跳过代理请求。

例如：对于浏览器请求，你想要提供一个 HTML 页面，但是对于 API 请求则保持代理。你可以这样做：

```js
proxy: {
  "/api": {
    target: "http://localhost:3000",
    bypass: function(req, res, proxyOptions) {
      if (req.headers.accept.indexOf("html") !== -1) {
        console.log("Skipping proxy for browser request.");
        return "/index.html";
      }
    }
  }
}
```

If you want to proxy multiple, specific paths to the same target, you can use an array of one or more objects with a `context` property:

```js
proxy: [{
  context: ["/auth", "/api"],
  target: "http://localhost:3000",
}]
```

## `devServer.progress` - 只用于命令行工具(CLI)

`boolean`

将运行进度输出到控制台。

```bash
webpack-dev-server --progress
```


## `devServer.public`

`string`

当使用*内联模式(inline mode)*并代理 dev-server 时，内联的客户端脚本并不总是知道要连接到什么地方。它会尝试根据 `window.location` 来猜测服务器的 URL，但是如果失败，你需要这样。

例如，dev-server 被代理到 nginx，并且在 `myapp.test` 上可用：

```js
public: "myapp.test:80"
```

Usage via the CLI

```bash
webpack-dev-server --public myapp.test:80
```


## `devServer.publicPath` 🔑

`string`

此路径下的打包文件可在浏览器中访问。

假设服务器运行在 `http://localhost:8080` 并且 `output.filename` 被设置为 `bundle.js`。默认 `publicPath` 是 `"/"`，所以你的包(bundle)可以通过 `http://localhost:8080/bundle.js` 访问。

可以修改 `publicPath`，将 bundle 放在一个目录：

```js
publicPath: "/assets/"
```

你的包现在可以通过 `http://localhost:8080/assets/bundle.js` 访问。

T> 确保 `publicPath` 总是以斜杠(/)开头和结尾。

也可以使用一个完整的 URL。这是模块热替换所必需的。

```js
publicPath: "http://localhost:8080/assets/"
```

bundle 可以通过 `http://localhost:8080/assets/bundle.js` 访问。

T> `devServer.publicPath` 和 `output.publicPath` 一样被推荐。


## `devServer.quiet` 🔑

`boolean`

启用 `quiet` 后，除了初始启动信息之外的任何内容都不会被打印到控制台。这也意味着来自 webpack 的错误或警告在控制台不可见。

```js
quiet: true
```

Usage via the CLI

```bash
webpack-dev-server --quiet
```


## `devServer.setup`

`function`

W> This option is __deprecated__ in favor of `before` and will be removed in v3.0.0.

Here you can access the Express app object and add your own custom middleware to it.
For example, to define custom handlers for some paths:

```js
setup(app){
  app.get('/some/path', function(req, res) {
    res.json({ custom: 'response' });
  });
}
```


## `devServer.socket`

`string`

使用Unix socket文件作监听 (代替主机端口模式).

```js
socket: 'socket'
```

通过命令行使用

```bash
webpack-dev-server --socket socket
```


## `devServer.staticOptions`

可以为从`contentBase`提供的静态页面提供高级选项配置，可以配置的选项参见[Express documentation](http://expressjs.com/en/4x/api.html#express.static)，例如：

```js
staticOptions: {
  redirect: false
}
```

T> 这仅在`contentBase`为一个字符串时才工作。


## `devServer.stats` 🔑

`string` `object`

This option lets you precisely control what bundle information gets displayed. This can be a nice middle ground if you want some bundle information, but not all of it.

To show only errors in your bundle:

```js
stats: "errors-only"
```

For more information, see the [**stats documentation**](/configuration/stats).

T> This option has no effect when used with `quiet` or `noInfo`.


## `devServer.stdin` - 仅CLI

`boolean`

This option closes the server when stdin ends.

```bash
webpack-dev-server --stdin
```


## `devServer.useLocalIp`

`boolean`

该选项使浏览器用你本地的IP打开

```js
useLocalIp: true
```

通过命令行使用

```bash
webpack-dev-server --useLocalIp
```


## `devServer.watchContentBase`

`boolean`

服务端监视在`devServer.contentBase`设置的文件， 文件改变触发页面刷新。

```js
watchContentBase: true
```

默认启用

通过命令行使用

```bash
webpack-dev-server --watch-content-base
```


## `devServer.watchOptions` 🔑

`object`

与监视文件相关的控制选项。

webpack 使用文件系统(file system)获取文件改动的通知。在某些情况下，不会正常工作。例如，当使用 Network File System (NFS) 时。[Vagrant](https://www.vagrantup.com/) 也有很多问题。在这些情况下，请使用轮询：

```js
watchOptions: {
  poll: true
}
```

如果这对文件系统来说太重了的话，你可以修改间隔时间（以毫秒为单位），将其设置为一个整数。

查看 [WatchOptions](/configuration/watch) 更多选项。

***

> 原文：https://webpack.js.org/configuration/dev-server/
