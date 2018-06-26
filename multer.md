先奉上 - [官方的中文翻译](https://github.com/expressjs/multer/blob/master/doc/README-zh-cn.md)

# Multer  
#### 1.3.0

Multer 是一个用于处理 `multipart/form-data` 数据类型的中间件，主要用于上传文件。为了达到最佳性能，Multer 选择基于 [busboy](https://github.com/mscdex/busboy) 进行开发。

NOTE: Multer 只处理 `multipart/form-data` 类型的数据。

## 安装

```
$ npm install --save multer
```

## 使用  

Multer 添加一个 `body` 对象和一个 `file` / `files` 对象到 `request` 对象中。 其中， `body` 对象中存放表单数据中的文本信息，`file` / `files` 对象中存放上传的文件信息。

### 基本用法

``` javascript
var express = require('express')
var multer  = require('multer')
var upload = multer({ dest: 'uploads/' })
 
var app = express()
 
app.post('/profile', upload.single('avatar'), function (req, res, next) {
  // req.file 存放着 'avatar' 的文件信息
  // req.body 存放这表单的文本信息，如果有的话
})
 
app.post('/photos/upload', upload.array('photos', 12), function (req, res, next) {
  // req.files 存放着 `photos` 的文件信息数组
  // req.body 存放这表单的文本信息，如果有的话
})
 
var cpUpload = upload.fields([{ name: 'avatar', maxCount: 1 }, { name: 'gallery', maxCount: 8 }])
app.post('/cool-profile', cpUpload, function (req, res, next) {
  // req.files 是一个对象，name 是key，存放 文件信息的数组 是值 { String: Array }
  //
  // 举例：
  //  req.files['avatar'][0] -> File
  //  req.files['gallery'] -> Array
  //
  // req.body 存放这表单的文本信息，如果有的话
})

```

在实际开发中，如果你想要处理只有文本的表单数据，你可以使用任何一种 Multer 方法（`.single()`, `.array()`, `.fields()`）。以 `.array()` 为例：

``` javascript
var express = require('express')
var app = express()
var multer  = require('multer')
var upload = multer()
 
app.post('/profile', upload.array(), function (req, res, next) {
  // req.body 存放这表单的文本信息
})
```

## API
### 文件信息  
每个上传的文件，所包含的信息：  

| 属性 | 描述 | 注意 |  
| ---- | ---- | ---- | 
| `fieldname` | Form表单的中指定的name |  |  
| `originalname` | 文件在用户机器上的名称 |  |
| `encoding` | 文件的编码类型 |  |  
| `mimetype` | 文件的 MIME 类型 |  |  
| `size` | 文件大小(字节) |  |  
| `destination` | 保存到哪个文件夹(dest设置的) | `DiskStorage` |
| `filename` | 保存在`destination`中的文件名称 | `DiskStorage` |  
| `path` | 已上传文件的完整路径 | `DiskStorage` |
| `buffer` | 整个文件的 `Buffer` | `MemoryStorage ` |  

### multer(options)  

Multer 接收一个对象 （`options`） 为参数，其中 `dest` 是该对象最基本的属性，决定了上传的文件所存放的位置。如果省略 `options` 对象，那么上传的文件将存在内存中，不会写到磁盘上。  

默认情况下，为了避免命名冲突， Multer 将对文件进行重命名。也可以根据你的需求，重写这个重命名方法。  

以下是参数对象 `options` 可以填写的属性。

| 属性 | 描述 |
| --- | ---- |
| `dest` 或 `storage` | 文件存储在哪里 |
| `fileFilter` | 通过编写 `function` 控制哪些文件被接受 |
| `limits` | 上传数据的限制 |
| `preservePath` | 保持文件的完整路径，而不只是名称 |


在大多数的web应用中，可能只需要配置 `dest` ，示例如下：  

``` javascript
var upload = multer({ dest: 'uploads/' })
```

如果你想在上传是添加更多的配置，那就用 `storage` 代替 `dest` 。Multer 有 `DiskStorage` 和 `MemoryStorage` 两个存储引擎；也可以通过第三方来获取更多的引擎。  

**`.single(fieldname)`**  

接受一个名称为 `fieldname` 的文件，这个文件的信息存储在 `req.file` 中。  

**`.array(fieldname[, maxCount])`**   

接受名称为 `fieldname` 的文件数组。如果上传文件数量超过 `maxCount` 将会报错。文件数组的信息，存放在 `req.files`中。

**`.fields(fields)`**  

接受一个由`fields `指定的，混合文件组。这些文件的信息（包含数组的对象），保存在 `req.files`中。  
`fields` 应该是一个对象数组，对象包含 `name` 和 `maxCount`两个属性  

```
[
  { name: 'avatar', maxCount: 1 },
  { name: 'gallery', maxCount: 8 }
]
``` 

**`.none()`**  

只接受文本域。如果有任何文件上传，会报 `LIMIT_UNEXPECTED_FILE ` 错。`.none()` 的作用与 `upload.fields([])`相同。  

**`.any()`**  

接收所有文件。文件信息将以数组的形式，存放在 `req.files`中。  
**警告：** 确保你会一直处理用户上传的文件。不要将 `multer` 全局引用，防止恶意上传文件，到非期望的路由上。哪个路由需要用到，就在哪个路由上使用。  

### `storage`  

**`DiskStorage`**


磁盘存储引擎让你在往磁盘存放文件时，有更多的可配置项。  

``` javascript
var storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, '/tmp/my-uploads')
  },
  filename: function (req, file, cb) {
    cb(null, file.fieldname + '-' + Date.now())
  }
})
 
var upload = multer({ storage: storage })
```

