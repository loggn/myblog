---
title: 对Gin的理解
description: 持续更新中
date: 2024-10-16T21:59:48+08:00
slug: 对Gin的理解
image: https://pic.netbian.com/uploads/allimg/240410/223535-17127597352a25.jpg
categories:
  - Gin
  - 理解输出
---

# 对 Gin 的理解

## Gin 简介

- 以下内容参考[Gin Web Framework](https://gin-gonic.com/zh-cn/)

this is a new test

**什么是 Gin?** Gin 是一个使用 Go 语言开发的 Web 框架。 它提供类似 Martini 的 API，但性能更佳，速度提升高达 40 倍。 如果你是性能和高效的追求者, 你会爱上 Gin。

### 特点

- 快速：基于 Radix 树的路由，小内存占用。没有反射。可预测的 API 性能。
- 支持中间件：传入的 HTTP 请求可以由一系列中间件和最终操作来处理。 例如：Logger，Authorization，GZIP，最终操作 DB。
- Crash 处理：Gin 可以 catch 一个发生在 HTTP 请求中的 panic 并 recover 它。这样，你的服务器将始终可用。例如，你可以向 Sentry 报告这个 panic！
- JSON 验证：Gin 可以解析并验证请求的 JSON，例如检查所需值的存在。
- 路由组：Gin 帮助您更好地组织您的路由，例如，按照需要授权和不需要授权和不同 API 版本进行分组。此外，路由分组可以无限嵌套而不降低性能。
- 错误管理：Gin 提供了一种方便的方法来收集 HTTP 请求期间发生的所有错误。最终，中间件可以将它们写入日志文件，数据库并通过网络发送。
- 内置渲染：Gin 为 JSON，XML 和 HTML 渲染提供了易于使用的 API。
- 可扩展性：新建一个中间件非常简单。

### 核心概念与功能

- Context 对象：gin.Context 是处理请求的核心对象，包含了请求和响应的所有信息。开发者可以通过它获取请求参数、查询参数、表单数据、JSON 数据等，同时也可以向前端返回响应。
- 路由定义：Gin 提供 RESTful 风格的路由支持，可以轻松定义 GET、POST、PUT、DELETE 等请求方法。路由还支持动态路径参数，如 c.Param() 可以获取路径中的参数。
- 请求绑定与验证：Gin 支持将请求的 JSON、表单数据等绑定到结构体，并提供验证功能，如通过 ShouldBindJSON() 绑定并校验请求参数。
- 文件上传：Gin 支持多部分表单数据（multipart form）的解析，处理文件上传操作非常简便。

### Gin 的应用场景

- Gin 适用于开发轻量级、高并发的 Web 服务和 API 项目，尤其是需要快速开发、性能优先的场景。
- 由于 Gin 的 API 设计简洁易用，并且扩展性强，它在微服务架构中也非常常见。

## 个简单的 Gin 应用

```
package main

import "github.com/gin-gonic/gin"

func main() {
  r := gin.Default() // 创建带有默认中间件的路由器(Logger和Recovery)

  // 定义一个 GET 路由，处理 /ping 请求
  r.GET("/ping", func(c *gin.Context) {
    //将结果以 JSON 格式返回给客户端。
    c.JSON(200, gin.H{
        "message": "pong",
    })
  })

  // 启动服务，监听在 8080 端口
  r.Run(":8080")
  }
```

### `r.GET("/ping", func(c *gin.Context){})`

- `GET`是请求方法
- `/ping`是路由，它可以根据需要自行定义
- `func(c *gin.Context)`是处理函数
- `c` 是上下文对象

---

### `c.JSON(200, gin.H{})`

- `c.JSON()`返回 JSON 格式的响应
- `200`状态码
- `gin.H{}`等同于`map[string]interface{}{...}`通常用于返回 JSON 数据或者向 HTML 模板传递数据。
  - ps:`interface{} `是 Go 语言中的万能类型，任何类型都可以作为它的值

## Gin 使用

### 带参路由与查询参数处理

```
r.GET("/ping/:studentID", func(c *gin.Context) {
		// 从 URL 中获取 studentID
		studentID := c.Param("studentID")
		// 获取查询参数 name
		name := c.Query("name")

    //数据处理逻辑...

    //返回响应内容
		c.JSON(200, gin.H{
			"isfind": "true",
		})
	})

```

### JSON 解析和绑定

```
type Login struct {
		Username string `json:"username"`
		Password string `json:"password"`
	}

	r.POST("/api/user/auth", func(c *gin.Context) {
		// 从请求体中读取 POST 表单参数
		var json Login
		// 绑定JSON数据到结构体
		if err := c.ShouldBindJSON(&json); err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return
		}
    //从结构体中拿出数据
		username := json.Username
		password := json.Password

    //处理数据

      //返回响应内容
			c.JSON(200, gin.H{
				"code":  0,
				"token": token,
			})

		} else {
			c.JSON(400, gin.H{
				"code":    -1,
				"message": "账号或者密码错误",
			})
		})

```

### 处理请求头中内容

- 这里以 Authorization 为例

```
r.GET("/api/user/data/student", func(c *gin.Context) {
		fmt.Print("1111")
		authHeader := c.GetHeader("Authorization")
		fmt.Println(authHeader)

		if authHeader == "" {
			c.JSON(401, gin.H{
				"code":    -1,
				"message": "未提供认证方式",
			})
			return
		}

		token := ""
		if len(authHeader) > 7 && authHeader[:7] == "Bearer " {
			token = authHeader[7:]
		} else {
			c.JSON(400, gin.H{
				"code":    -1,
				"message": "无效的认证方式",
			})
			return
		}

		valid, err := utils.VerifyToken(token)
		if err != nil || !valid {
			c.JSON(403, gin.H{
				"code":    -1,
				"message": "无效的token",
			})
			return
		}

		var users []utils.StudentNew
		result := utils.DB.Find(&users)
		if result.Error != nil {
			c.JSON(400, gin.H{
				"code":    -1,
				"message": "查询出错",
			})
			return
		} else {
			c.JSON(200, users)
		}
	})
```

### 文件接收与处理

```
r.POST("/api/file/data/import/excel", func(c *gin.Context) {
		file, err := c.FormFile("file")
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": "文件上传失败"})
			return
		}

		// 保存文件到本地
		filePath := filepath.Join("./", file.Filename)
		if err := c.SaveUploadedFile(file, filePath); err != nil {
			c.JSON(http.StatusInternalServerError, gin.H{"error": "文件保存失败"})
			return
		}

		// 解析文件并存储到数据库
		if err := utils.ParseAndSaveExcel(filePath); err != nil {
			c.JSON(http.StatusInternalServerError, gin.H{"error": "文件解析失败", "details": err.Error()})
			return
		}

    //返回响应内容
		c.JSON(http.StatusOK, gin.H{
			"code": 0,
			"data": filePath,
		})
	})
```

### 路由组(网上内容，后期替换)

```
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	router := gin.Default()

	// 路由组
	v1 := router.Group("/api/v1")
	{
		v1.GET("/users", func(c *gin.Context) {
			c.String(200, "List of users")
		})
		v1.POST("/users", func(c *gin.Context) {
			c.String(200, "Create a user")
		})
		v1.PUT("/users/:id", func(c *gin.Context) {
			id := c.Param("id")
			c.String(200, "Update user with ID: %s", id)
		})
		v1.DELETE("/users/:id", func(c *gin.Context) {
			id := c.Param("id")
			c.String(200, "Delete user with ID: %s", id)
		})
	}

	router.Run(":8080")
}
```

### 模板渲染(网上内容，后期替换)

```
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	router := gin.Default()

	// 加载模板文件
	router.LoadHTMLGlob("templates/*")

	// 定义路由处理函数，渲染模板
	router.GET("/hello", func(c *gin.Context) {
		c.HTML(http.StatusOK, "hello.tmpl", gin.H{
			"title": "Hello, Gin!",
		})
	})

	router.Run(":8080")
}
```

### 静态文件服务

```
package main

import "github.com/gin-gonic/gin"

func main() {
	router := gin.Default()

	// 从相对路径 "assets" 提供静态文件
	router.Static("/static", "./assets")

	// 从绝对路径 "/tmp" 提供静态文件
	router.StaticFS("/static2", http.Dir("/tmp"))

	// 提供单个静态文件
	router.StaticFile("/favicon.ico", "./resources/favicon.ico")

	router.Run(":8080")
}
```

## 补充知识

### `c *gin.Context`的方法

- `c.ShouldBindJSON()`获取 JSON 格式数据
- `c.Param()`从 URL 中获取参数
- `c.Query()`获取查询参数
- `c.FormFile()`获取文件
- `c.GetHeader()`获取请求头
- `c.SaveUploadedFile()`保存文件

### 请求方法与路由

#### 常用的 HTTP 方法有

- GET：用于从服务器获取资源。
- POST：用于向服务器发送数据或提交表单。
- PUT：用于更新服务器上的资源。
- DELETE：用于删除服务器上的资源。

#### 路由

- 静态路由：简单的固定路径，比如`/ping`
- 动态路由：也叫参数化路由，路径中包含变量占位符，比如`/ping/:id`
  - 当用户访问 /user/123 时，Gin 会解析 :id 并将其值作为参数传递给处理函数。在处理函数中通过 c.Param("id") 获取该值

### 状态码

#### HTTP 状态码分类：

- 1xx - 信息性响应（Informational Responses）： 表示请求已经被接收，继续处理。
  - 100 Continue：服务器已经接收到请求的初始部分，客户端可以继续发送请求的其余部分。
  - 101 Switching Protocols：请求者要求服务器切换协议，服务器确认切换。
- 2xx - 成功（Success）： 表示请求成功并被服务器处理。
  - 200 OK：请求成功，服务器返回所请求的数据。
  - 201 Created：请求已成功，并且资源已经被创建（常见于 POST 请求创建新资源的场景）。
  - 204 No Content：请求成功，但没有返回内容（如执行了 DELETE 请求后没有内容需要返回）。
- 3xx - 重定向（Redirection）： 表示客户端需要进一步操作才能完成请求，通常是重定向。

  - 301 Moved Permanently：资源的 URL 已被永久更改，客户端应使用新的 URL。
  - 302 Found：资源临时重定向到新的 URL。
  - 304 Not Modified：资源未被修改，客户端可以使用缓存的版本。

- 4xx - 客户端错误（Client Errors）： 表示请求存在错误，客户端需要修改请求才能继续。

  - 400 Bad Request：请求有语法错误，服务器无法理解。
  - 401 Unauthorized：请求未经授权，需进行身份验证。
  - 403 Forbidden：服务器拒绝执行请求，即使请求经过身份验证。
  - 404 Not Found：请求的资源不存在或无法找到。
  - 405 Method Not Allowed：请求的 HTTP 方法（如 GET、POST）不被允许。

- 5xx - 服务器错误（Server Errors）： 表示服务器在处理请求时发生了内部错误。
  - 500 Internal Server Error：服务器遇到未知错误，无法处理请求。
  - 501 Not Implemented：服务器不支持请求所需的功能。
  - 502 Bad Gateway：服务器作为网关或代理，从上游服务器接收到无效响应。
  - 503 Service Unavailable：服务器暂时无法处理请求，通常是因为服务器超载或维护。

### 常见的状态码解释：

- 200 OK：表示请求正常处理并返回成功。
- 400 Bad Request：表示客户端发送的请求有问题，通常是因为格式错误。
- 401 Unauthorized：表示用户没有权限访问请求的资源，通常需要身份验证。
- 403 Forbidden：表示请求被服务器拒绝，用户没有权限，即使身份验证通过。
- 404 Not Found：表示请求的资源不存在，可能是 URL 错误。
- 500 Internal Server Error：表示服务器内部错误，导致请求无法完成。
