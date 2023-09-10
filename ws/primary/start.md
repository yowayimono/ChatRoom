<!--
 * @Author: yowayimono
 * @Date: 2023-06-12 14:05:52
 * @LastEditors: yowayimono
 * @LastEditTime: 2023-06-12 14:06:22
 * @Description: nothing
-->
以下是对给定代码的逐行分析，并附上中文注释：

```go
package primary

import (
	"github.com/gin-gonic/gin"
	"github.com/spf13/viper"
	"go-gin-chat/ws"
	"go-gin-chat/ws/go_ws"
)

// 定义 serve 的映射关系
var serveMap = map[string]ws.ServeInterface{
	"Serve":   &ws.Serve{},
	"GoServe": &go_ws.GoServe{},
}

// 创建并返回一个实现了 ServeInterface 接口的对象
func Create() ws.ServeInterface {
	// 获取配置文件中的 serve_type 配置
	// 可以是 Serve 或 GoServe
	_type := viper.GetString("app.serve_type")
	return serveMap[_type] // 根据 serve_type 返回对应的对象
}

// 启动 WebSocket 服务
func Start(gin *gin.Context)  {
	Create().RunWs(gin) // 调用 Create 函数返回的对象的 RunWs 方法
}

// 获取在线用户数
func OnlineUserCount() int {
	return Create().GetOnlineUserCount() // 调用 Create 函数返回的对象的 GetOnlineUserCount 方法
}

// 获取指定房间的在线用户数
func OnlineRoomUserCount(roomId int) int {
	return Create().GetOnlineRoomUserCount(roomId) // 调用 Create 函数返回的对象的 GetOnlineRoomUserCount 方法
}
```

总结：

该模块主要提供了 WebSocket 服务相关的功能。它包含了创建和管理 WebSocket 服务的对象，根据配置文件中的 `serve_type` 创建对应的对象，以及提供了启动 WebSocket 服务、获取在线用户数和获取指定房间在线用户数的方法。其中，根据配置的 `serve_type`，它会选择使用 `ws.Serve` 或 `go_ws.GoServe` 对象来处理 WebSocket 服务。通过调用相应对象的方法，可以实现 WebSocket 服务的启动和获取在线用户数的功能。

在某些情况下，需要使用不同的 WebSocket 服务器来满足特定的需求或应对不同的场景。以下是一些可能需要多个不同的 WebSocket 服务器的场景：

1. 性能要求不同：不同的 WebSocket 服务器实现可能在性能方面有所差异。对于高并发、大规模的应用场景，可能需要选择性能更高的 WebSocket 服务器，以确保服务器能够处理大量的连接和消息交互。而对于低并发、小规模的应用场景，可能可以使用性能较低但更轻量的 WebSocket 服务器。

2. 功能需求不同：不同的 WebSocket 服务器实现可能具有不同的功能和特性。某些服务器可能提供额外的功能，如消息广播、群组管理、权限控制等。根据应用的需求，可能需要选择适合的 WebSocket 服务器以满足这些功能要求。

3. 扩展性和集成：在某些情况下，可能需要将 WebSocket 服务器与其他系统或服务进行集成。不同的服务器可能具有更好的扩展性和集成能力，可以更容易地与现有的技术栈或第三方服务进行集成。

4. 特定业务场景：不同的业务场景可能对 WebSocket 服务器有特定的要求。例如，某些场景可能需要支持实时数据处理和推送，而另一些场景可能更关注可靠性和数据持久化。选择适合的 WebSocket 服务器可以更好地满足特定业务场景的需求。

通过灵活选择使用不同的 WebSocket 服务器，可以根据具体需求来优化系统的性能、功能和集成能力，从而提高应用的效率和可扩展性。