## cors  

#### 2.8.4  

CORS 是一个[Connect/Express](https://github.com/senchalabs/connect#readme) 中间件，通过传入参数，实现[CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)。

作者的Twitter [@troygoode](https://twitter.com/intent/user?screen_name=troygoode)!  

* 安装
* 使用
	* 简单使用
	* 单个路由使用CORS
	* 配置CORS
	* 配置异步的CORS
* 配置项
* 例子
* License
* 作者信息

## 安装  

``` javascript
$ npm i cors

```

## 使用  

#### 简单使用（应用于所有请求）  

``` javascript
var express = require('express')
var cors = require('cors')
var app = express()
 
app.use(cors())
 
app.get('/products/:id', function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for all origins!'})
})
 
app.listen(80, function () {
  console.log('CORS-enabled web server listening on port 80')
})
```  

#### 单个路由上应用CORS  

``` javascript
var express = require('express')
var cors = require('cors')
var app = express()
 
app.get('/products/:id', cors(), function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for a Single Route'})
})
 
app.listen(80, function () {
  console.log('CORS-enabled web server listening on port 80')
})
```  

#### 配置CORS  

``` javascript
var express = require('express')
var cors = require('cors')
var app = express()
 
var corsOptions = {
  origin: 'http://example.com',
  optionsSuccessStatus: 200 // some legacy browsers (IE11, various SmartTVs) choke on 204
}
 
app.get('/products/:id', cors(corsOptions), function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for only example.com.'})
})
 
app.listen(80, function () {
  console.log('CORS-enabled web server listening on port 80')
})
```  

#### 配置CORS动态域 `Origin`  

``` javascript
var express = require('express')
var cors = require('cors')
var app = express()
 
var whitelist = ['http://example1.com', 'http://example2.com']
var corsOptions = {
  origin: function (origin, callback) {
    if (whitelist.indexOf(origin) !== -1) {
      callback(null, true)
    } else {
      callback(new Error('Not allowed by CORS'))
    }
  }
}
 
app.get('/products/:id', cors(corsOptions), function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for a whitelisted domain.'})
})
 
app.listen(80, function () {
  console.log('CORS-enabled web server listening on port 80')
})
```  

#### CORS 处理预请求  

一些CORS请求是复杂的，需要一个 `OPTIONS` 请求（预请求）。一个复杂的CORS请求，用一个HTTP谓词 （如： DETELE），而不是 GET/HEAD/POST，或者是自定义头部。为了处理预请求，你必须添加一个新的路由，去支持OPTIONS的回调：  

``` javascript
var express = require('express')
var cors = require('cors')
var app = express()
 
 // 为 DELETE 请求，支持预请求
app.options('/products/:id', cors()) 
app.del('/products/:id', cors(), function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for all origins!'})
})
 
app.listen(80, function () {
  console.log('CORS-enabled web server listening on port 80')
})
```  

你也可以这样处理预请求： 

``` javascript
app.options('*', cors()) // 包含之前所有的路由
```  

#### 配置异步CORS  

``` javascript
var express = require('express')
var cors = require('cors')
var app = express()
 
var whitelist = ['http://example1.com', 'http://example2.com']
var corsOptionsDelegate = function (req, callback) {
  var corsOptions;
  if (whitelist.indexOf(req.header('Origin')) !== -1) {
    corsOptions = { origin: true } // CORS response 的起禁用
  }else{
    corsOptions = { origin: false } // CORS request 起禁用
  }
  callback(null, corsOptions) // 回调中传入 error 和 options
}
 
app.get('/products/:id', cors(corsOptionsDelegate), function (req, res, next) {
  res.json({msg: 'This is CORS-enabled for a whitelisted domain.'})
})
 
app.listen(80, function () {
  console.log('CORS-enabled web server listening on port 80')
})
```  

## 配置项  

* `origin`: 配置CORS **Access-Control-Allow-Origin** 头。可能的值：  
  * `Boolean` 设置 `origin` 为 `true` 来启用请求域，通过 `req.header('Origin')`设置，或者设置为 `false` 来禁用CORS。  
  * `String` 设置 `origin` 为一个指定的域。例如：如果你设置为 `"http://example.com"` 那么只有来自 "http://example.com" 的请求会被允许。  
  * `RegExp` 设置 `origin` 为正则表达式，来检验请求域。如果匹配，那么将会被处理。例如：设置为 `/example\.com$/`，那么所有以 "example.com" 结尾的，都会响应。  
  * `Array` 设置 `origin` 为一个数组，可以包含`String`和`RegExp`，如：`["http://example1.com", /\.example2\.com$/]` ，接受所有来自 `"http://example1.com"` 或者 主域为 `example2.com`的请求。    
  * `Function`  设置 `origin` 为一个方法，实现一些自定义的逻辑。请求域为第一个参数，回调方法（参数为 err:对象 和 allow: 布尔值）为第二个参数。  
* `methods`: 配置CORS **Access-Control-Allow-Methods** 头。用一个逗号分隔的字符串（ `'GET,PUT,POST'`）或者数组 （`['GET', 'PUT', 'POST']`）  
* `allowedHeaders`: 配置CORS **Access-Control-Allow-Headers** 头。值为一个用逗号隔开的字符串（ 'Content-Range,X-Content-Range'）或者一个数组（`['Content-Range', 'X-Content-Range']`）。如果没有配置，默认与请求头相同。  
* `exposedHeaders`: 配置CORS **Access-Control-Expose-Headers** 头。值为一个用逗号隔开的字符串（'Content-Range,X-Content-Range'）或者一个数组（`['Content-Range', 'X-Content-Range']`）。如果定义，那么就没有自定义的头部。  
* `credentials`: 配置CORS **Access-Control-Allow-Credentials** 头。设置`true`，来传递头部，否则将被忽略。  
* `maxAge`: 配置CORS **Access-Control-Max-Age** 头。传递一个整数，否则忽略。  
* `preflightContinue`：传递CORS预请求的响应，到下一个回调函数。  
* `optionsSuccessStatus`: 为成功的`OPTIONS`请求提供一个状态码，因为部分浏览器（IE11, various SmartTVs）阻塞在 `204`。  

``` javascript
{
  "origin": "*",
  "methods": "GET,HEAD,PUT,PATCH,POST,DELETE",
  "preflightContinue": false,
  "optionsSuccessStatus": 204
}
```  

关于CORS头部更详细的，看[这里](https://www.html5rocks.com/en/tutorials/cors/)  

## Demo  

用jQuery实现的例子：[http://node-cors-client.herokuapp.com/](https://node-cors-client.herokuapp.com/)  

上面那个例子的代码：  
* 客户端：[https://github.com/TroyGoode/node-cors-client](https://github.com/TroyGoode/node-cors-client)  
* 服务端：[https://github.com/TroyGoode/node-cors-server](https://github.com/TroyGoode/node-cors-server)  

## 作者  

[Troy Goode](https://github.com/TroyGoode) (troygoode@gmail.com)