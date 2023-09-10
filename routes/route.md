1. 导入必要的包：
```go
import (
	"github.com/gin-gonic/gin"
	"github.com/spf13/viper"
	"go-gin-chat/controller"
	"go-gin-chat/services/session"
	"go-gin-chat/static"
	"go-gin-chat/ws/primary"
	"net/http"
)
```
代码中导入了Gin框架、viper配置库、控制器、会话服务、静态文件、WebSocket等所需的包。

1. 定义初始化路由函数：
```go
func InitRoute() *gin.Engine {
	router := gin.New()
```
函数名为`InitRoute`，返回类型为`*gin.Engine`，表示初始化并返回一个Gin引擎实例。在函数内部创建了一个新的Gin引擎实例，该实例将用于处理HTTP请求。

1. 根据配置设置静态文件路径：
```go
if viper.GetString(`app.debug_mod`) == "false" {
	router.StaticFS("/static", http.FS(static.EmbedStatic))
} else {
	router.StaticFS("/static", http.Dir("static"))
}
```
根据配置文件中的`app.debug_mod`值判断当前是否为调试模式。如果不是调试模式，则使用嵌入的静态文件系统（通过`http.FS(static.EmbedStatic)`）作为静态文件路径。否则，使用本地的静态文件夹（"static"）作为静态文件路径。

1. 创建路由组和路由：
```go
sr := router.Group("/", session.EnableCookieSession())
```
使用`router.Group()`方法创建一个名为`sr`的路由组，将所有的路由路径前缀设置为`"/"`。`session.EnableCookieSession()`是一个中间件函数，用于启用Cookie会话。

1. 定义路由和处理函数：
```go
sr.GET("/", controller.Index)
sr.POST("/login", controller.Login)
sr.GET("/logout", controller.Logout)
sr.GET("/ws", primary.Start)
```
定义了一系列路由和对应的处理函数。例如，使用`GET`方法请求`"/"`路径时，将调用`controller.Index`函数处理请求。其他路由类似，分别对应登录、登出和WebSocket等功能。

1. 添加身份验证中间件的路由组：
```go
authorized := sr.Group("/", session.AuthSessionMiddle())
```
使用`sr.Group()`方法创建一个名为`authorized`的路由组，将其路由路径前缀设置为`"/"`。`session.AuthSessionMiddle()`是一个中间件函数，用于验证用户身份的会话。

1. 定义需要身份验证的路由和处理函数：
```go
authorized.GET("/home", controller.Home)
authorized.GET("/room/:room_id", controller.Room)
authorized.GET("/private-chat", controller.PrivateChat)
authorized.POST("/img-kr-upload", controller.ImgKrUpload)
authorized.GET("/pagination", controller.Pagination)
```
在`authorized`路由组中，定义了一系列需要进行身份验证的路由和对应的处理函数。

最后，通过返回创建的`router`实例，将初始化好的路由引

擎返回给调用方。

