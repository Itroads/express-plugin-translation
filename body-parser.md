## body-parser  
#### 1.18.3  

Node.js 请求体解析中间件。  

在你的逻辑处理之前，解析传入中间件的请求体，解析后的信息放在 `req.body`。  

**注意** `req.body`的数据格式依据用户输入的格式，这个对象中的所有属性和方法，不能拿过来直接用，要先做验证。举例：`req.body.foo.toString()` 就有多种报错的可能，比如 `foo` 属性可能根本就不存在，或者不是一个字符串，`toString ` 可能不是一个方法，而是一个字符串或者用户输入的其他的什么东西。  

[了解Node.js中的HTTP是如何工作](https://nodejs.org/en/docs/guides/anatomy-of-an-http-transaction/)。