先奉上 - [官方的中文翻译](https://github.com/expressjs/multer/blob/master/doc/README-zh-cn.md)

## Multer  
#### 1.3.0

Multer 是一个用于处理 `multipart/form-data` 数据类型的中间件，主要用于上传文件。为了性能的最大化，Multer 基于 [busboy](https://github.com/mscdex/busboy) 编写。

NOTE: Multer 只处理 `multipart/form-data` 类型的数据。

### 安装

```
$ npm install --save multer
```

### 使用  

Multer 添加一个 `body` 对象和一个 `file` / `files` 对象到 `request` 对象中。 其中， `body` 对象中存放表单数据中的文本信息，`file` / `files` 对象中存放上传的文件信息。

