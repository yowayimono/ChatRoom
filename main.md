<!--
 * @Author: yowayimono
 * @Date: 2023-06-11 15:48:19
 * @LastEditors: yowayimono
 * @LastEditTime: 2023-06-11 15:48:45
 * @Description: nothing
-->
1. 导入必要的包：
```go
import (
	"bytes"
	"github.com/gin-gonic/gin"
	"github.com/spf13/viper"
	"go-gin-chat/conf"
	"go-gin-chat/models"
	"go-gin-chat/routes"
	"go-gin-chat/views"
	"go-gin-chat/ws/go_ws"
	"log"
	"net/http"
)
```
代码中导入了Gin框架、viper配置库、自定义的配置文件、数据模型、路由、视图和WebSocket等所需的包。

1. 初始化函数：
```go
func init() {
	viper.SetConfigType("json") // 设置配置文件的类型

	if err := viper.ReadConfig(bytes.NewBuffer(conf.AppJsonConfig)); err != nil {
		// 处理配置文件读取错误
		log.Fatal(err) // 读取配置文件失败致命错误
	}

	models.InitDB() // 初始化数据库连接
}
```
在`init`函数中进行一些初始化操作。首先，设置配置文件的类型为JSON。然后使用`viper.ReadConfig`方法读取配置文件，并通过`conf.AppJsonConfig`提供的配置内容创建一个`bytes.Buffer`实例。如果读取配置文件失败，则输出错误信息并终止程序。接下来，调用`models.InitDB()`方法初始化数据库连接。

1. 主函数：
```go
func main() {
	gin.SetMode(gin.ReleaseMode) // 关闭debug模式

	port := viper.GetString(`app.port`) // 从配置中获取端口号
	router := routes.InitRoute() // 初始化路由

	router.SetHTMLTemplate(views.GoTpl) // 加载模板文件

	go_ws.CleanOfflineConn() // 清理离线连接

	log.Println("监听端口", "http://127.0.0.1:"+port)

	http.ListenAndServe(":"+port, router) // 启动HTTP服务器
}
```
在`main`函数中，首先通过`gin.SetMode`方法关闭Gin框架的调试模式。然后从配置中获取端口号。接下来，调用`routes.InitRoute()`方法初始化路由，获取一个Gin路由引擎实例。使用`router.SetHTMLTemplate`方法加载模板文件。然后调用`go_ws.CleanOfflineConn()`方法清理离线连接。最后，使用`http.ListenAndServe`方法启动HTTP服务器，监听指定的端口。

这段代码的功能是初始化应用程序配置、数据库连接和路由，然后启动一个HTTP服务器来处理请求。
