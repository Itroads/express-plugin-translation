## body-parser  
#### 1.18.3  

Node.js 请求体解析中间件。  

在你的逻辑处理之前，解析传入中间件的请求体，解析后的信息放在 `req.body`。  

**注意** `req.body`的数据格式依据用户输入的格式，这个对象中的所有属性和方法，不能拿过来直接用，要先做验证。举例：`req.body.foo.toString()` 就有多种报错的可能，比如 `foo` 属性可能根本就不存在，或者不是一个字符串，`toString ` 可能不是一个方法，而是一个字符串或者用户输入的其他的什么东西。  

[了解Node.js中的HTTP是如何工作](https://nodejs.org/en/docs/guides/anatomy-of-an-http-transaction/)。  

由于上传数据的复杂性和通常体积比较大的本质，导致不支持多个数据体，下面的这些模块，你可能感兴趣：  

* [busboy](https://www.npmjs.com/package/busboy#readme) 和 [connect-busboy](https://www.npmjs.com/package/connect-busboy#readme)  
* [multiparty](https://www.npmjs.com/package/multiparty#readme) 和 [connect-multiparty](https://www.npmjs.com/package/connect-multiparty#readme)  
* [formidable](https://www.npmjs.com/package/formidable#readme)  
* [multer](https://www.npmjs.com/package/multer#readme)  

`body-parser` 支持一下数据类型的解析：

* [JSON body parser](#bodyparserjsonoptions)  
* [Raw body parser](#bodyparserrawoptions)  
* [Text body parser](#bodyparsertextoptions)  
* [URL-encoded form body parser](#bodyparserurlencodedoptions)  

其他你可能感兴趣的请求体解析工具：

* [body](https://www.npmjs.com/package/body#readme)
* [co-body](https://www.npmjs.com/package/co-body#readme) 

## 安装  

```
$ npm install body-parser
```

## API  

``` javascript
var bodyParser = require('body-parser')
```

`bodyParser`对象公开各种工厂方法，来创建中间件。所有的中间件，通过匹配 `Content-Type` 请求头，然后解析对应的携带信息（`body`），最后将解析获得的信息，放到 `req.body`中，如果没有 `body` 则req.body 为一个空对象 （`{}`），如果没有匹配到对应的`Content-Type` 请求头，那么就会发生错误。  

这个模块返回的各种错误，在[本文的错误部分](#errors) 有相应的描述。  

### bodyParser.json([options])  

返回一个只解析 `json` 格式的中间件，当请求头中 `Content-Type` 与`tpye`选项匹配的时候。这个解析器接受任何 `Unicode` 编码，并且支持`gzip`的解压和`deflate`编码。  

中间件处理后的数据，放到`request` 对象中。如：`req.body`  

##### Options  

`.json` 方法接受一个可选的 `options` 对象，可以包含下面的任意一项：  

**inflate**  

当设置为 `true` 压缩的 `body` 将被解压；设置为 `false` 后，压缩的 `body` 将被拒绝。默认值为 `true`。  

**limit**  

控制请求体的大小。如果是一个数字，那么允许的大小就是对应数字的字节数；如果是一个字符串，将值传给 [bytes](https://www.npmjs.com/package/bytes) 库解析。默认为 `100kb`。  

**reviver**  

`reviver` 直接传递给 `JSON.parse` 作为第二个参数。你可以获得更多信息在[MDN关于JSON.parse的文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse#Example.3A_Using_the_reviver_parameter)  

**strict**  

设置为`true`，将只接受数组和对象；设置为 `false`，接受任何`JSON.parse` 可以解析的数据。默认为 `true`。  

**type**  

`type`选项用来确定中间件将要解析的媒体类型。这个选项，可以是字符串，字符串数组或者是一个`function`。如果不是一个`function`，`type`选项将直接传入 [type-is](https://www.npmjs.com/package/type-is#readme)库，可以是一个扩展名（像 `json`），一个MIME类型（像 `application/json`），或者一个带有通配符的 MIME类型（像 `*/*` 或 `*/json`）。如果是一个`function`，`type`选项则被声明为 `fn(req)` ，如果返回一个真实的值，那么请求将被解析。默认值 `application/json`。  

**verify**  

`verify` 选项，如果以`verify(req, res, buf, encoding)`方式提供，其中，`buf`是一个原生请求提的 `Buffer`,`encoding`是请求的编码。可以通过抛出错误，来终止解析。  

### bodyParser.raw([options])  

返回一个将所有请求体都解析为 `Buffer`的中间件，只关注 `Content-Type` 与 `type`选项匹配的请求。这个解析器接受任何 `Unicode` 编码，并且支持`gzip`的解压和`deflate`编码。  

经过中间件解析之后，一个新的包含解析数据的`body`对象，存放到`request`对象中（如：`req.body`）。是请求体中的一个 `Buffer`对象。

##### Options  

`.raw` 方法接受一个可选的 `options` 对象，可以包含下面的任意一项： 

**inflate**  

当设置为 `true` 压缩的 `body` 将被解压；设置为 `false` 后，压缩的 `body` 将被拒绝。默认值为 `true`。  

**limit**  

控制请求体的大小。如果是一个数字，那么允许的大小就是对应数字的字节数；如果是一个字符串，将值传给 [bytes](https://www.npmjs.com/package/bytes) 库解析。默认为 `100kb`。  

**type**  

`type`选项用来确定中间件将要解析的媒体类型。这个选项，可以是字符串，字符串数组或者是一个`function`。如果不是一个`function`，`type`选项将直接传入 [type-is](https://www.npmjs.com/package/type-is#readme)库，可以是一个扩展名（像 `bin`），一个MIME类型（像 `application/octet-stream`），或者一个带有通配符的 MIME类型（像 `*/*` 或 `application/*`）。如果是一个`function`，`type`选项则被声明为 `fn(req)` ，如果返回一个真实的值，那么请求将被解析。默认值 `application/octet-stream`。  

**verify**  

`verify` 选项，如果以`verify(req, res, buf, encoding)`方式提供，其中，`buf`是一个原生请求提的 `Buffer`,`encoding`是请求的编码。可以通过抛出错误，来终止解析。  

### bodyParser.text([options])  

返回一个将所有请求体都解析为 `String`的中间件，只关注 `Content-Type` 与 `type`选项匹配的请求。这个解析器接受任何 `Unicode` 编码，并且支持`gzip`的解压和`deflate`编码。  

经过中间件解析之后，一个新的包含解析数据的`body`对象，存放到`request`对象中（如：`req.body`）。是请求体中的一个 `String`对象。  

##### Options  

`.raw` 方法接受一个可选的 `options` 对象，可以包含下面的任意一项：  

**defaultCharset**  

如果没有在 `Content-Type`中指定字符集，那么就会采用默认字符集。默认值 `utf-8`  

**inflate**  

当设置为 `true` 压缩的 `body` 将被解压；设置为 `false` 后，压缩的 `body` 将被拒绝。默认值为 `true`。  

**limit**  

控制请求体的大小。如果是一个数字，那么允许的大小就是对应数字的字节数；如果是一个字符串，将值传给 [bytes](https://www.npmjs.com/package/bytes) 库解析。默认为 `100kb`。  

**type**  

`type`选项用来确定中间件将要解析的媒体类型。这个选项，可以是字符串，字符串数组或者是一个`function`。如果不是一个`function`，`type`选项将直接传入 [type-is](https://www.npmjs.com/package/type-is#readme)库，可以是一个扩展名（像 `txt`），一个MIME类型（像 `text/plain`），或者一个带有通配符的 MIME类型（像 `*/*` 或 `text/*`）。如果是一个`function`，`type`选项则被声明为 `fn(req)` ，如果返回一个真实的值，那么请求将被解析。默认值 `text/plain`。  

**verify**  

`verify` 选项，如果以`verify(req, res, buf, encoding)`方式提供，其中，`buf`是一个原生请求提的 `Buffer`,`encoding`是请求的编码。可以通过抛出错误，来终止解析。  

### bodyParser.urlencoded([options])  

返回一个将所有请求体都解析为 `String`的中间件，只关注 `Content-Type` 与 `type`选项匹配的请求。这个解析器只接受 `UTF-8` 编码，并且支持`gzip`的解压和`deflate`编码。  

经过中间件解析之后，一个新的包含解析数据的`body`对象，存放到`request`对象中（如：`req.body`）。当 `extended` 为 `false` 时，这个对象将包含键值对，值可能是字符串或者数组，当`extended` 为 `true`是，值可以是任意类型。  

##### Options  

`. urlencoded` 方法接受一个可选的 `options` 对象，可以包含下面的任意一项：  

**extended**  

`extended`选项允许选择用 `querystring`库（`false`）或者 `qs`库（`true`）来解析 `URL-encoded`数据。“extended” 语法允许富对象和数组编码为 `URL-encoded`格式，`URL-encoded`就像JSON。更多的信息，看[查看qs库](https://www.npmjs.com/package/qs#readme)  

默认值为 `true`, 但是不推荐使用默认值。请研究`qs`和`querystring`的差别，然后选择适当的设置。  

**inflate**  

当设置为 `true` 压缩的 `body` 将被解压；设置为 `false` 后，压缩的 `body` 将被拒绝。默认值为 `true`。  

**limit**  

控制请求体的大小。如果是一个数字，那么允许的大小就是对应数字的字节数；如果是一个字符串，将值传给 [bytes](https://www.npmjs.com/package/bytes) 库解析。默认为 `100kb`。  

**parameterLimit**  

`parameterLimit`选项可控制URL-encoded数据中，允许传入参数个数的最大数。如果一个请求的参数超过了这个设置的值，回返回413给客户端。默认值 `1000`。

**type**  

`type`选项用来确定中间件将要解析的媒体类型。这个选项，可以是字符串，字符串数组或者是一个`function`。如果不是一个`function`，`type`选项将直接传入 [type-is](https://www.npmjs.com/package/type-is#readme)库，可以是一个扩展名（像 `urlencoded `），一个MIME类型（像 `application/x-www-form-urlencoded`），或者一个带有通配符的 MIME类型（像 `*/x-www-form-urlencoded`）。如果是一个`function`，`type`选项则被声明为`fn(req)` ，如果返回一个真实的值，那么请求将被解析。默认值 `application/x-www-form-urlencoded`。  

**verify**  

`verify` 选项，如果以`verify(req, res, buf, encoding)`方式提供，其中，`buf`是一个原生请求提的 `Buffer`,`encoding`是请求的编码。可以通过抛出错误，来终止解析。  

## Errors  

这个模块提供的中间件，根据解析过程中的错误条件来抛出错误。这些错误通常有一个 `status/statusCode`属性，包含在HTTP响应码，`expose`属性决定，`message`是否应该展现到客户端，`type`属性在不匹配`message`的情况下确定错误类型，`body`属性如果可用，将包含读取的请求体信息。  

下面是一些常见的错误，尽管不同的原因可能引起不同的错误。  

### content encoding unsupported  

这个错误将会触发，当请求头中包含 `Content-Encoding`，但是“inflation”设置为 `false`。`status`属性设置为 `415`，`type`属性设置为 `'encoding.unsupported'`,`charset`设置为不支持的编码。

### request aborted  

这个错误将要被触发，当客户端读取完成之前终止请求。`received`属性将被设置为已经接收的字节数在请求终端之前，`expected`属性被设置为期望的字节数，`status` 设置为 `400`，`type` 属性设置为 `request.aborted`。  

### request entity too large  

这个错误将要被触发，当请求体的大小大于 'limit'选项设置的。`limit` 属性将被设置为字节限制，`length`属性将被设置为请求体的长度。`status`属性将被设置为413，`type` 属性被设置为 `entity.too.large`。  

### request size did not match content length  

这个错误将要被触发，当请求的长度和`Content-Length`请求头的长度不匹配。这通常发生在请求畸形时，通常发生在`Content-Length`基于字符计算，而不是基于字节计算时。`status` 属性设置为`400`，`type`属性设置为`'request.size.invalid'`。  

### stream encoding should not be set  

这个错误将要被触发，当在这个中间件之前调用 `req.setEncoding` 方法。这个模块直接操作字节，当使用这个模块时，你不能调用`req.setEncoding`方法。`status` 属性设置为`500`，`type`属性设置为`'stream.encoding.set'`。  

### too many parameters  

这个错误将要被触发，当请求的内容超过 `urlencoded`解析的`parameterLimit`配置。`status` 属性设置为`413`，`type`属性设置为`'parameters.too.many'`。  

### unsupported charset "BOGUS"  

这个错误将要被触发，当请求有一个字符集参数 `Content-Type`，但是 `iconv-lite`模块不支持，或者解析器不支持。字符集包含在信息中，也包含在`charset`属性中。`status` 属性设置为`415`，`type`属性设置为`'charset.unsupported'`。`charset`设置为不支持的字符集。  

### unsupported content encoding "bogus"  

这个错误将要被触发，当请求包含一个 `Content-Encoding`头，但是不支持。编码包含在信息中也包含在`encoding`属性中。`status` 属性设置为`415`，`type`属性设置为`'encoding.unsupported'`，`encoding`属性设置为不支持的编码。  

## 例子  

### Express/Connect top-level generic  

添加一个普通的`JSON`和`URL-encoded`解析器，作为顶层中间件，将要解析所有的请求。这是最简单的设置。  

``` javascript
var express = require('express')
var bodyParser = require('body-parser')

var app = express()

// 解析 application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))

// 解析 application/json
app.use(bodyParser.json())

app.use(function (req, res) {
  res.setHeader('Content-Type', 'text/plain')
  res.write('you posted:\n')
  res.end(JSON.stringify(req.body, null, 2))
})
```

### Express route-specific  

在需要的路由上添加解析器。这是在Express中使用 body-parser 最推荐的使用方法。  

``` javascript
var express = require('express')
var bodyParser = require('body-parser')

var app = express()

// 创建 application/json 解析器
var jsonParser = bodyParser.json()

// 创建 application/x-www-form-urlencoded 解析器
var urlencodedParser = bodyParser.urlencoded({ extended: false })

// POST /login 获得 urlencoded 请求体
app.post('/login', urlencodedParser, function (req, res) {
  if (!req.body) return res.sendStatus(400)
  res.send('welcome, ' + req.body.username)
})

// POST /api/users 获得 JSON 请求体
app.post('/api/users', jsonParser, function (req, res) {
  if (!req.body) return res.sendStatus(400)
  // create user in req.body
})
```

### Change accepted type for parsers  

所有的解析器都有一个 `type`选项，允许更改中间件的解析类型 `Content-Type`。  

``` javascript
var express = require('express')
var bodyParser = require('body-parser')

var app = express()

// 将各种不同的自定义的JSON类型解析为JSON
app.use(bodyParser.json({ type: 'application/*+json' }))

// 将一些自定义的东西解析为 Buffer
app.use(bodyParser.raw({ type: 'application/vnd.custom-type' }))

// 将HTML解析为字符串
app.use(bodyParser.text({ type: 'text/html' }))
```  

## License  

[MIT](https://github.com/expressjs/body-parser/blob/master/LICENSE)  

