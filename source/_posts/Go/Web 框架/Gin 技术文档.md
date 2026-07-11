---
title: Gin 技术文档
date: 2025-09-26 13:55:34
top_img: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/Web%20%E6%A1%86%E6%9E%B6/Gin%20%E6%8A%80%E6%9C%AF%E6%96%87%E6%A1%A3/preview.jpg
cover: https://simon-blog-bucket.oss-cn-shanghai.aliyuncs.com/Go/Web%20%E6%A1%86%E6%9E%B6/Gin%20%E6%8A%80%E6%9C%AF%E6%96%87%E6%A1%A3/preview.jpg
tags:
  - Go
  - Gin
categories: Go
---



# 介绍



Gin 是一个用 Go (Golang) 编写的 Web 框架。 它具有类似 martini 的 API，性能要好得多，多亏了 httprouter，速度提高了 40 倍。 如果您需要性能和良好的生产力，您一定会喜欢 Gin。Gin 相比于 Iris 和 Beego 而言，更倾向于轻量化的框架，只负责 Web 部分，追求极致的路由性能，功能或许没那么全，胜在轻量易拓展，这也是它的优点。因此，在所有的 Web 框架中，Gin 是最容易上手和学习的

官方文档：[Gin Web Framework (gin-gonic.com)](https://gin-gonic.com/zh-cn/)

仓库地址：[gin-gonic/gin: Gin is a HTTP web framework written in Go (Golang)](https://github.com/gin-gonic/gin)

官方示例：[gin-gonic/examples: A repository to host examples and tutorials for Gin. (github.com)](https://github.com/gin-gonic/examples)



------

# 特性



* 快速：基于 Radix 树的路由，小内存占用。没有反射。可预测的 API 性能
* 支持中间件：传入的 HTTP 请求可以由一系列中间件和最终操作来处理。 例如：Logger，Authorization，GZIP，最终操作 DB
* Crash 处理：Gin 可以 catch 一个发生在 HTTP 请求中的 panic 并 recover 它。这样，你的服务器将始终可用
* JSON 验证：Gin 可以解析并验证请求的 JSON，例如检查所需值的存在
* 路由组：更好地组织路由。是否需要授权，不同的 API 版本…… 此外，这些组可以无限制地嵌套而不会降低性能
* 错误管理：Gin 提供了一种方便的方法来收集 HTTP 请求期间发生的所有错误。最终，中间件可以将它们写入日志文件，数据库并通过网络发送
* 内置渲染：Gin 为 JSON，XML 和 HTML 渲染提供了易于使用的 API
* 可扩展性：新建一个中间件非常简单



------

# 快速开始



1. 下载依赖

   ```sh
   go get -u github.com/gin-gonic/gin
   ```

2. 编写代码

   ```go
   package main
   
   import (
      "github.com/gin-gonic/gin"
      "net/http"
   )
   
   func main() {
      engine := gin.Default() //创建gin引擎
      engine.GET("/ping", func(context *gin.Context) {
         context.JSON(http.StatusOK, gin.H{
            "message": "pong",
         })
      })
      engine.Run() //开启服务器，默认监听localhost:8080
   }
   ```

3. 发起请求

   ```http
   GET localhost:8080/ping
   ```

4. 响应结果

   ```http
   HTTP/1.1 200 OK
   Content-Type: application/json; charset=utf-8
   Date: Tue, 22 Nov 2023 08:47:11 GMT
   Content-Length: 18
   
   {
     "message": "pong"
   }
   Response file saved.
   > 2023-11-22T164711.200.json
   ```



------

# 参数解析



Gin 中的参数解析总共支持三种方式: 路由参数，URL 参数，表单参数



------

## 路由参数



路由参数其实是封装了 HttpRouter 的参数解析功能，使用方法基本上与 HttpRouter 一致

```go
package main

import (
   "github.com/gin-gonic/gin"
   "log"
   "net/http"
)

func main() {
   e := gin.Default()
   e.GET("/findUser/:username/:userid", FindUser)
   e.GET("/downloadFile/*filepath", UserPage)

   log.Fatalln(e.Run(":8080"))
}

// 命名参数示例
func FindUser(c *gin.Context) {
   username := c.Param("username")
   userid := c.Param("userid")
   c.String(http.StatusOK, "username is %s\n userid is %s", username, userid)
}

// 路径参数示例
func UserPage(c *gin.Context) {
   filepath := c.Param("filepath")
   c.String(http.StatusOK, "filepath is  %s", filepath)
}
```

```
// 示例一
curl --location --request GET '127.0.0.1:8080/findUser/jack/001'

username is jack
userid is 001
```

```
// 示例二
curl --location --request GET '127.0.0.1:8080/downloadFile/img/fruit.png'

filepath is  /img/fruit.png
```



------

## URL 参数



传统的 URL 参数，格式就是 /url?key=val&key1=val1&key2=val2

```go
package main

import (
   "github.com/gin-gonic/gin"
   "log"
   "net/http"
)

func main() {
   e := gin.Default()
   e.GET("/findUser", FindUser)
   log.Fatalln(e.Run(":8080"))
}

func FindUser(c *gin.Context) {
   username := c.DefaultQuery("username", "defaultUser")
   userid := c.Query("userid")
   c.String(http.StatusOK, "username is %s\nuserid is %s", username, userid)
}
```

```
// 示例一
curl --location --request GET '127.0.0.1:8080/findUser?username=jack&userid=001'

username is jack
userid is 001
```

```
// 示例二
curl --location --request GET '127.0.0.1:8080/findUser'

username is defaultUser
userid is
```



------

## 表单参数



表单的内容类型一般有 application/json，application/x-www-form-urlencoded，application/xml，multipart/form-data

```go
package main

import (
  "github.com/gin-gonic/gin"
  "net/http"
)

func main() {
  e := gin.Default()
  e.POST("/register", RegisterUser)
  e.POST("/update", UpdateUser)
  e.Run(":8080")
}

func RegisterUser(c *gin.Context) {
  username := c.PostForm("username")
  password := c.PostForm("password")
  c.String(http.StatusOK, "successfully registered,your username is [%s],password is [%s]", username, password)
}

func UpdateUser(c *gin.Context) {
  var form map[string]string
  c.ShouldBind(&form)
  c.String(http.StatusOK, "successfully update,your username is [%s],password is [%s]", form["username"], form["password"])
}
```

```
// 示例一 使用 form-data
// PostForm 方法默认解析 application/x-www-form-urlencoded 和 multipart/form-data 类型的表单
curl --location --request POST '127.0.0.1:8080/register' \
--form 'username="jack"' \
--form 'password="123456"'

successfully registered,your username is [jack],password is [123456]
```

```
// 示例二 使用 JSON
curl --location --request POST '127.0.0.1:8080/update' \
--header 'Content-Type: application/json' \
--data-raw '{
    "username":"username",
    "password":"123456"
}'

successfully update,your username is [username],password is [123456]
```



------

# 数据解析



在大多数情况下，我们都会使用结构体来承载数据，而不是直接解析参数。在 gin 中，用于数据绑定的方法主要是 Bind()和 ShouldBind()，两者的区别在于前者内部也是直接调用的 ShouldBind()，前者返回 err 时，会直接进行 400 响应，后者则不会。如果想要更加灵活的进行错误处理，建议选择后者。这两个函数会自动根据请求的 content-type 来进行推断用什么方式解析

```go
func (c *Context) MustBindWith(obj any, b binding.Binding) error {
    // 调用了ShouldBindWith()
  if err := c.ShouldBindWith(obj, b); err != nil {
    c.AbortWithError(http.StatusBadRequest, err).SetType(ErrorTypeBind) // 直接响应400 badrequest
    return err
  }
  return nil
}
```

如果想要自行选择可以使用 BindWith()和 ShouldBindWith()，例如

```go
c.MustBindWith(obj, binding.JSON) //json
c.MustBindWith(obj, binding.XML) //xml
```

Gin 支持的绑定类型有如下几种实现：

```go
var (
   JSON          = jsonBinding{}
   XML           = xmlBinding{}
   Form          = formBinding{}
   Query         = queryBinding{}
   FormPost      = formPostBinding{}
   FormMultipart = formMultipartBinding{}
   ProtoBuf      = protobufBinding{}
   MsgPack       = msgpackBinding{}
   YAML          = yamlBinding{}
   Uri           = uriBinding{}
   Header        = headerBinding{}
   TOML          = tomlBinding{}
)
```

```go
package main

import (
  "fmt"
  "github.com/gin-gonic/gin"
  "net/http"
)

type LoginUser struct {
  Username string `bind:"required" json:"username" form:"username" uri:"username"`
  Password string `bind:"required" json:"password" form:"password" uri:"password"`
}

func main() {
    e := gin.Default()
  e.POST("/loginWithJSON", Login)
  e.POST("/loginWithForm", Login)
  e.GET("/loginWithQuery/:username/:password", Login)
  e.Run(":8080")
}

func Login(c *gin.Context) {
  var login LoginUser
    // 使用ShouldBind来让gin自动推断
  if c.ShouldBind(&login) == nil && login.Password != "" && login.Username != "" {
    c.String(http.StatusOK, "login successfully !")
  } else {
    c.String(http.StatusBadRequest, "login failed !")
  }
  fmt.Println(login)
}
```

<br>

当使用 URL 参数时，这种方式就会报错了，因为 URL 数据绑定的 Content-Type 为空，这时就需要手动指定解析方式

```go
if err := c.ShouldBindUri(&login); err == nil && login.Password != "" && login.Username != "" {
   c.String(http.StatusOK, "login successfully !")
} else {
   fmt.Println(err)
   c.String(http.StatusBadRequest, "login failed !")
}
```

<br>

一般方法都是通过调用 c.Request.Body 方法绑定数据，但不能多次调用这个方法，例如 c.ShouldBind，不可重用，如果想要多次绑定的话，可以使用 c.ShouldBindBodyWith

```go
func SomeHandler(c *gin.Context) {
  objA := formA{}
  objB := formB{}
  // 读取 c.Request.Body 并将结果存入上下文。
  if errA := c.ShouldBindBodyWith(&objA, binding.JSON); errA == nil {
    c.String(http.StatusOK, `the body should be formA`)
  // 这时, 复用存储在上下文中的 body。
  }
  if errB := c.ShouldBindBodyWith(&objB, binding.JSON); errB == nil {
    c.String(http.StatusOK, `the body should be formB JSON`)
  // 可以接受其他格式
  }
  if errB2 := c.ShouldBindBodyWith(&objB, binding.XML); errB2 == nil {
    c.String(http.StatusOK, `the body should be formB XML`)
  }
}
```

c.ShouldBindBodyWith 会在绑定之前将 body 存储到上下文中。 这会对性能造成轻微影响，如果调用一次就能完成绑定的话，那就不要用这个方法。只有某些格式需要此功能，如 JSON, XML, MsgPack, ProtoBuf。 对于其他格式, 如 Query, Form, FormPost, FormMultipart 可以多次调用 c.ShouldBind() 而不会造成任何性能损失



------

# 数据校验



Gin 内置的校验工具其实是 github.com/go-playground/validator/v10，使用方法也几乎没有什么差别

```go
type LoginUser struct {
   Username string `binding:"required"  json:"username" form:"username" uri:"username"`
   Password string `binding:"required" json:"password" form:"password" uri:"password"`
}

func main() {
   e := gin.Default()
   e.POST("/register", Register)
   log.Fatalln(e.Run(":8080"))
}

func Register(ctx *gin.Context) {
   newUser := &LoginUser{}
   if err := ctx.ShouldBind(newUser); err == nil {
      ctx.String(http.StatusOK, "user%+v", *newUser)
   } else {
      ctx.String(http.StatusBadRequest, "invalid user,%v", err)
   }
}
```

```sh
curl --location --request POST 'http://localhost:8080/register' \
--header 'Content-Type: application/json' \
--data-raw '{
    "username":"jack1"

}'
```

```
invalid user,Key: 'LoginUser.Password' Error:Field validation for 'Password' failed on the 'required' tag
```

需要注意的一点是，gin 中 validator 的校验 tag 是 binding，而单独使用 validator 的的校验 tag 是 validator



------

# 数据响应



数据响应是接口处理中最后一步要做的事情，后端将所有数据处理完成后，通过 HTTP 协议返回给调用者，Gin 对于数据响应提供了丰富的内置支持，用法简洁明了，上手十分容易



------

## 简单示例



```go
func Hello(c *gin.Context) {
    // 返回纯字符串格式的数据，http.StatusOK代表着200状态码，数据为"Hello world !"
  c.String(http.StatusOK, "Hello world !")
}
```



------

## HTML 渲染



文件加载的时候，默认根路径是项目路径，也就是 go.mod 文件所在的路径，下面例子中的 index.html 即位于根路径下的 index.html，不过一般情况下这些模板文件都不会放在根路径，而是会存放在静态资源文件夹中

```go
func main() {
   e := gin.Default()
    // 加载HTML文件，也可以使用Engine.LoadHTMLGlob()
   e.LoadHTMLFiles("index.html")
   e.GET("/", Index)
   log.Fatalln(e.Run(":8080"))
}

func Index(c *gin.Context) {
   c.HTML(http.StatusOK, "index.html", gin.H{})
}
```

```sh
curl --location --request GET 'http://localhost:8080/'
```

```HTML
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>GinLearn</title>
  </head>

  <body>
    <h1>Hello World!</h1>
    <h1>This is a HTML Template Render Example</h1>
  </body>
</html>
```



------

## 快速响应



前面经常用到 context.String() 方法来进行数据响应，这是最原始的响应方法，直接返回一个字符串，Gin 中其实还内置了许多了快速响应的方法例如：

```go
// 使用Render写入响应头，并进行数据渲染
func (c *Context) Render(code int, r render.Render)

// 渲染一个HTML模板，name是html路径，obj是内容
func (c *Context) HTML(code int, name string, obj any)

// 以美化了的缩进JSON字符串进行数据渲染，通常不建议使用这个方法，因为会造成更多的传输消耗。
func (c *Context) IndentedJSON(code int, obj any)

// 安全的JSON，可以防止JSON劫持，详情了解：https://www.cnblogs.com/xusion/articles/3107788.html
func (c *Context) SecureJSON(code int, obj any)

// JSONP方式进行渲染
func (c *Context) JSONP(code int, obj any)

// JSON方式进行渲染
func (c *Context) JSON(code int, obj any)

// JSON方式进行渲染，会将unicode码转换为ASCII码
func (c *Context) AsciiJSON(code int, obj any)

// JSON方式进行渲染，不会对HTML特殊字符串进行转义
func (c *Context) PureJSON(code int, obj any)

// XML方式进行渲染
func (c *Context) XML(code int, obj any)

// YML方式进行渲染
func (c *Context) YAML(code int, obj any)

// TOML方式进行渲染
func (c *Context) TOML(code int, obj interface{})

// ProtoBuf方式进行渲染
func (c *Context) ProtoBuf(code int, obj any)

// String方式进行渲染
func (c *Context) String(code int, format string, values ...any)

// 重定向到特定的位置
func (c *Context) Redirect(code int, location string)

// 将data写入响应流中
func (c *Context) Data(code int, contentType string, data []byte)

// 通过reader读取流并写入响应流中
func (c *Context) DataFromReader(code int, contentLength int64, contentType string, reader io.Reader, extraHeaders map[string]string)

// 高效的将文件写入响应流
func (c *Context) File(filepath string)

// 以一种高效的方式将fs中的文件流写入响应流
func (c *Context) FileFromFS(filepath string, fs http.FileSystem)

// 以一种高效的方式将fs中的文件流写入响应流，并且在客户端会以指定的文件名进行下载
func (c *Context) FileAttachment(filepath, filename string)

// 将服务端推送流写入响应流中
func (c *Context) SSEvent(name string, message any)

// 发送一个流响应并返回一个布尔值，以此来判断客户端是否在流中间断开
func (c *Context) Stream(step func(w io.Writer) bool) bool
```

对于大多数应用而言，用的最多的还是 context.JSON，其他的相对而言要少一些



------

## 异步处理



在 Gin 中，异步处理需要结合 goroutine 使用，使用起来十分简单

```go
// copy返回一个当前Context的副本以便在当前Context作用范围外安全的使用，可以用于传递给一个goroutine
func (c *Context) Copy() *Context
```

```go
func main() {
  e := gin.Default()
  e.GET("/hello", Hello)
  log.Fatalln(e.Run(":8080"))
}

func Hello(c *gin.Context) {
  ctx := c.Copy()
  go func() {
    // 子协程应该使用Context的副本，不应该使用原始Context
    log.Println("异步处理函数: ", ctx.HandlerNames())
  }()
  log.Println("接口处理函数: ", c.HandlerNames())
  c.String(http.StatusOK, "hello")
}
```

```sh
curl --location --request GET 'http://localhost:8080/hello'
```

```
2022/12/21 13:33:47 异步处理函数:  []
2022/12/21 13:33:47 接口处理函数:  [github.com/gin-gonic/gin.LoggerWithConfig.func1 github.com/gin-gonic/gin.CustomRecoveryWithWriter.func1 main.Hello]
[GIN] 2022/12/21 - 13:33:47 | 200 |     11.1927ms |             ::1 | GET      "/hello"
```

可以看到两者输出不同，副本在复制时，为了安全考虑，删掉了许多元素的值



------

## 文件传输



文件传输是 Web 应用的一个不可或缺的功能，gin 对于此的支持也是封装的十分简单，但其实本质上和用原生的 net/http 的流程都差不多。流程都是从请求体中读取文件流，然后再保存到本地



------

## 单文件上传



```go
func main() {
  e := gin.Default()
  e.POST("/upload", uploadFile)
  log.Fatalln(e.Run(":8080"))
}

func uploadFile(ctx *gin.Context) {
  // 获取文件
  file, err := ctx.FormFile("file")
  if err != nil {
    ctx.String(http.StatusBadRequest, "%+v", err)
    return
  }
  // 保存在本地
  err = ctx.SaveUploadedFile(file, "./"+file.Filename)
  if err != nil {
    ctx.String(http.StatusBadRequest, "%+v", err)
    return
  }
  // 返回结果
  ctx.String(http.StatusOK, "upload %s size:%d byte successfully!", file.Filename, file.Size)
}
```

```sh
curl --location --request POST 'http://localhost:8080/upload' \
--form 'file=@"/C:/Users/user/Pictures/Camera Roll/a.jpg"'
```

```
upload a.jpg size:1424 byte successfully!
```



------

## 多文件上传



```go
func main() {
   e := gin.Default()
   e.POST("/upload", uploadFile)
   e.POST("/uploadFiles", uploadFiles)
   log.Fatalln(e.Run(":8080"))
}

func uploadFiles(ctx *gin.Context) {
  // 获取gin解析好的multipart表单
  form, _ := ctx.MultipartForm()
  // 根据键值取得对应的文件列表
  files := form.File["files"]
  // 遍历文件列表，保存到本地
  for _, file := range files {
    err := ctx.SaveUploadedFile(file, "./"+file.Filename)
    if err != nil {
      ctx.String(http.StatusBadRequest, "upload failed")
      return
    }
  }
  // 返回结果
  ctx.String(http.StatusOK, "upload %d files successfully!", len(files))
}
```

```sh
curl --location --request POST 'http://localhost:8080/uploadFiles' \
--form 'files=@"/C:/Users/Stranger/Pictures/Camera Roll/a.jpg"' \
--form 'files=@"/C:/Users/Stranger/Pictures/Camera Roll/123.jpg"' \
--form 'files=@"/C:/Users/Stranger/Pictures/Camera Roll/girl.jpg"'
```

```
upload 3 files successfully!
```



------

## 文件下载



关于文件下载的部分 Gin 对于原有标准库的 API 再一次封装，使得文件下载异常简单

```go
func main() {
  e := gin.Default()
  e.GET("/download/:filename", download)
  log.Fatalln(e.Run(":8080"))
}

func download(ctx *gin.Context) {
    // 获取文件名
  filename := ctx.Param("filename")
    // 返回对应文件
  ctx.FileAttachment(filename, filename)
}
```

```sh
curl --location --request GET 'http://localhost:8080/download/a.jpg'
```

```
Content-Disposition: attachment; filename="a.jpg"
Date: Wed, 21 Dec 2022 08:04:17 GMT
Last-Modified: Wed, 21 Dec 2022 07:50:44 GMT
```



------

# 路由管理

## 路由组



创建一个路由组是将接口分类，不同类别的接口对应不同的功能，也更易于管理

```go
func Hello(c *gin.Context) {

}

func Login(c *gin.Context) {

}

func Update(c *gin.Context) {

}

func Delete(c *gin.Context) {

}
```

在创建分组的时候，也可以给分组的根路由注册处理器，不过大多数时候并不会这么做

```go
func main() {
  e := gin.Default()
  v1 := e.Group("v1")
  {
    v1.GET("/hello", Hello)
    v1.GET("/login", Login)
  }
  v2 := e.Group("v2")
  {
    v2.POST("/update", Update)
    v2.DELETE("/delete", Delete)
  }
}
```

这里将其分成了 v1，v2 两个分组，其中的花括号 {} 仅仅只是为了规范，表名花括号内注册的处理器是属于同一个路由分组，在功能上没有任何作用。同样的，gin 也支持嵌套分组，方法与上例一致



------

## 404 路由



gin 中的 Engine 结构体提供了一个方法 NoRoute，来设置当访问的 URL 不存在时如何处理，开发者可以将逻辑写入此方法中，以便路由未找到时自动调用，默认会返回 404 状态码

```go
func main() {
   e := gin.Default()
   v1 := e.Group("v1")
   {
      v1.GET("/hello", Hello)
      v1.GET("/login", Login)
   }
   v2 := e.Group("v2")
   {
      v2.POST("/update", Update)
      v2.DELETE("/delete", Delete)
   }
   // 注册处理器
   e.NoRoute(func(context *gin.Context) { 
      // 这里只是演示，不要在生产环境中直接返回HTML代码
      context.String(http.StatusNotFound, "<h1>404 Page Not Found</h1>")
   })
   log.Fatalln(e.Run(":8080"))
}
```

随便发一个请求

```sh
curl --location --request GET 'http://localhost:8080/'
```

```html
<h1>404 Page Not Found</h1>
```



------

## 405 路由



Http 状态码中，405 代表着当前请求的方法类型是不允许的，Gin 中提供了如下方法

```go
func (engine *Engine) NoMethod(handlers ...HandlerFunc)
```

来注册一个处理器，以便在发生时自动调用，前提是设置 Engine.HandleMethodNotAllowed = true

```go
func main() {
   e := gin.Default()
   // 需要将其设置为true
   e.HandleMethodNotAllowed = true
   v1 := e.Group("/v1")
   {
      v1.GET("/hello", Hello)
      v1.GET("/login", Login)
   }
   v2 := e.Group("/v2")
   {
      v2.POST("/update", Update)
      v2.DELETE("/delete", Delete)
   }
   e.NoRoute(func(context *gin.Context) {
      context.String(http.StatusNotFound, "<h1>404 Page Not Found</h1>")
   })
   // 注册处理器
   e.NoMethod(func(context *gin.Context) {
      context.String(http.StatusMethodNotAllowed, "method not allowed")
   })
   log.Fatalln(e.Run(":8080"))
}
```

配置好后，Gin 默认的 header 是不支持 OPTION 请求的，测试一下

```sh
curl --location --request OPTIONS 'http://localhost:8080/v2/delete'
```

```
method not allowed
```



------

## 重定向



Gin 中的重定向十分简单，调用 gin.Context.Redirect() 方法即可

```go
func main() {
  e := gin.Default()
  e.GET("/", Index)
  e.GET("/hello", Hello)
  log.Fatalln(e.Run(":8080"))
}

func Index(c *gin.Context) {
  c.Redirect(http.StatusMovedPermanently, "/hello")
}

func Hello(c *gin.Context) {
  c.String(http.StatusOK, "hello")
}
```

```
curl --location --request GET 'http://localhost:8080/'
```

```
hello
```



------

# 中间件

## 简介



Gin 十分轻便灵活，拓展性非常高，对于中间件的支持也非常友好。在 Gin 中，所有的接口请求都要经过中间件，通过中间件，开发者可以自定义实现很多功能和逻辑，Gin 虽然本身自带的功能很少，但是由第三方社区开发的 Gin 拓展中间件十分丰富

中间件本质上其实还是一个接口处理器

```go
// HandlerFunc defines the handler used by gin middleware as return value.
type HandlerFunc func(*Context)
```

从某种意义上来说，每一个请求对应的处理器也是中间件，只不过是作用范围非常小的局部中间件

```go
func Default() *Engine {
   debugPrintWARNINGDefault()
   engine := New()
   engine.Use(Logger(), Recovery())
   return engine
}
```

查看 Gin 的源代码，Default 函数中，返回的默认 Engine 就使用两个默认中间件 Logger()，Recovery()，如果不想使用默认的中间件也可以使用 gin.New() 来代替



------

## 全局中间件



全局中间件即作用范围为全局，整个系统所有的请求都会经过此中间件

```go
func GlobalMiddleware() gin.HandlerFunc {
   return func(ctx *gin.Context) {
      fmt.Println("全局中间件被执行...")
   }
}
```

先创建一个闭包函数来创建中间件，再通过 Engine.Use() 来注册全局中间件

```go
func main() {
   e := gin.Default()
   // 注册全局中间件
   e.Use(GlobalMiddleware())
   v1 := e.Group("/v1")
   {
      v1.GET("/hello", Hello)
      v1.GET("/login", Login)
   }
   v2 := e.Group("/v2")
   {
      v2.POST("/update", Update)
      v2.DELETE("/delete", Delete)
   }
   log.Fatalln(e.Run(":8080"))
}
```

```sh
curl --location --request GET 'http://localhost:8080/v1/hello'
```

```
全局中间件被执行...
```



------

## 局部中间件



局部中间件即作用范围为局部，系统中局部的请求会经过此中间件。局部中间件可以注册到单个路由上，不过更多时候是注册到路由组上

```go
func main() {
   e := gin.Default()
   // 注册全局中间件
   e.Use(GlobalMiddleware())
   // 注册路由组局部中间件
   v1 := e.Group("/v1", LocalMiddleware())
   {
      v1.GET("/hello", Hello)
      v1.GET("/login", Login)
   }
   v2 := e.Group("/v2")
   {
      // 注册单个路由局部中间件
      v2.POST("/update", LocalMiddleware(), Update)
      v2.DELETE("/delete", Delete)
   }
   log.Fatalln(e.Run(":8080"))
}
```

```sh
curl --location --request POST 'http://localhost:8080/v2/update'
```

```
全局中间件被执行...
局部中间件被执行
```



------

## 中间件原理



Gin 中间的使用和自定义非常容易，其内部的原理也比较简单，为了后续的学习，需要简单的了解下内部原理。Gin 中的中间件其实用到了责任链模式，Context 中维护着一个 HandlersChain，本质上是一个 []HandlerFunc，和一个 index，其数据类型为 int8。在 Engine.handlerHTTPRequest(c *Context) 方法中，有一段代码表明了调用过程：Gin 在路由树中找到了对应的路由后，便调用了 Next()方法

```go
if value.handlers != nil {
   // 将调用链赋值给Context
   c.handlers = value.handlers
   c.fullPath = value.fullPath
   // 调用中间件
   c.Next()
   c.writermem.WriteHeaderNow()
   return
}
```

Next()的调用才是关键，Next() 会遍历路由的 handlers 中的 HandlerFunc 并执行，此时可以看到 index 的作用就是记录中间件的调用位置。其中，给对应路由注册的接口函数也在 handlers 内，这也就是为什么前面会说接口也是一个中间件

```go
func (c *Context) Next() {
   // 一进来就+1是为了避免陷入递归死循环，默认值是-1
   c.index++
   for c.index < int8(len(c.handlers)) {
      // 执行HandlerFunc
      c.handlers[c.index](c)
      // 执行完毕，index+1
      c.index++
   }
}
```

修改一下 Hello() 的逻辑，来验证是否果真如此

```go
func Hello(c *gin.Context) {
   fmt.Println(c.HandlerNames())
}
```

```
[github.com/gin-gonic/gin.LoggerWithConfig.func1 github.com/gin-gonic/gin.CustomRecoveryWithWriter.func1 main.GlobalMiddleware.func1 main.LocalMiddleware.func1 main.Hello]
```

可以看到中间件调用链的顺序为：Logger -> Recovery -> GlobalMiddleware -> LocalMiddleWare -> Hello，调用链的最后一个元素才是真正要执行的接口函数，前面的都是中间件

<br>

在注册局部路由时，有如下一个断言

```go
finalSize := len(group.Handlers) + len(handlers) //中间件总数
assert1(finalSize < int(abortIndex), "too many handlers")
```

其中 abortIndex int8 = math.MaxInt8 >> 1 值为 63，即使用系统时路由注册数量不要超过 63 个



------

## 计时器中间件



在知晓了上述的中间件原理后，就可以编写一个简单的请求时间统计中间件

```go
func TimeMiddleware() gin.HandlerFunc {
   return func(context *gin.Context) {
      // 记录开始时间
      start := time.Now()
      // 执行后续调用链
      context.Next()
      // 计算时间间隔
      duration := time.Since(start)
      // 输出纳秒，以便观测结果
      fmt.Println("请求用时: ", duration.Nanoseconds())
   }
}

func main() {
  e := gin.Default()
  // 注册全局中间件，计时中间件
  e.Use(GlobalMiddleware(), TimeMiddleware())
  // 注册路由组局部中间件
  v1 := e.Group("/v1", LocalMiddleware())
  {
    v1.GET("/hello", Hello)
    v1.GET("/login", Login)
  }
  v2 := e.Group("/v2")
  {
    // 注册单个路由局部中间件
    v2.POST("/update", LocalMiddleware(), Update)
    v2.DELETE("/delete", Delete)
  }
  log.Fatalln(e.Run(":8080"))
}
```

```
curl --location --request GET 'http://localhost:8080/v1/hello'
```

```
请求用时:  517600
```

一个简单的计时器中间件就已经编写完毕了



------

# 服务配置



光是使用默认的配置是远远不够的，大多数情况下都需求修改很多的服务配置才能达到需求



------

## HTTP 配置



可以通过 net/http 创建 Server 来配置，Gin 本身也支持像原生 API 一样使用 Gin

```go
func main() {
   router := gin.Default()
   server := &http.Server{
      Addr:           ":8080",
      Handler:        router,
      ReadTimeout:    10 * time.Second,
      WriteTimeout:   10 * time.Second,
      MaxHeaderBytes: 1 << 20,
   }
   log.Fatal(server.ListenAndServe())
}
```



------

## 静态资源配置



静态资源在以往基本上是服务端不可或缺的一部分，尽管在现在使用占比正在逐渐减少，但仍旧有大量的系统还是使用单体架构的情况

<br>

Gin 提供了三个方法来加载静态资源

```go
// 加载某一静态文件夹
func (group *RouterGroup) Static(relativePath, root string) IRoutes

// 加载某一个fs
func (group *RouterGroup) StaticFS(relativePath string, fs http.FileSystem) IRoutes

// 加载某一个静态文件
func (group *RouterGroup) StaticFile(relativePath, filepath string) IRoutes
```

<br>

假设项目的目录如下

```
root
|
|-- static
|  |
|  |-- a.jpg
|  |
|  |-- favicon.ico
|
|-- view
  |
  |-- html
```

```go
func main() {
   router := gin.Default()
   // 加载静态文件目录
   router.Static("/static", "./static")
   // 加载静态文件目录
   router.StaticFS("/view", http.Dir("view"))
   // 加载静态文件
   router.StaticFile("/favicon", "./static/favicon.ico")

   router.Run(":8080")
}
```



------

## 跨域配置



Gin 本身是没有对于跨域配置做出任何处理，需要自行编写中间件来进行实现相应的需求，其实难度也不大，稍微熟悉 HTTP 协议的人一般都能写出来，逻辑基本上都是那一套

```go
func CorsMiddle() gin.HandlerFunc {
   return func(c *gin.Context) {
      method := c.Request.Method
      origin := c.Request.Header.Get("Origin")
      if origin != "" {
         // 生产环境中的服务端通常都不会填 *，应当填写指定域名
         c.Header("Access-Control-Allow-Origin", origin)
         // 允许使用的HTTP METHOD
         c.Header("Access-Control-Allow-Methods", "POST, GET, OPTIONS, PUT, DELETE, UPDATE")
         // 允许使用的请求头
         c.Header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept, Authorization")
         // 允许客户端访问的响应头
         c.Header("Access-Control-Expose-Headers", "Content-Length, Access-Control-Allow-Origin, Access-Control-Allow-Headers, Cache-Control, Content-Language, Content-Type")
         // 是否需要携带认证信息 Credentials 可以是 cookies、authorization headers 或 TLS client certificates
         // 设置为true时，Access-Control-Allow-Origin不能为 *
         c.Header("Access-Control-Allow-Credentials", "true")
      }
      // 放行OPTION请求，但不执行后续方法
      if method == "OPTIONS" {
         c.AbortWithStatus(http.StatusNoContent)
      }
      // 放行
      c.Next()
   }
}
```

将中间件注册为全局中间件即可

