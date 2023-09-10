<!--
 * @Author: yowayimono
 * @Date: 2023-06-11 17:52:11
 * @LastEditors: yowayimono
 * @LastEditTime: 2023-06-11 17:52:18
 * @Description: nothing
-->
这段代码是一个消息服务包的简单封装，包含了两个函数。

1. `GetLimitMsg(roomId string, offset int) []map[string]interface{}`：该函数调用了 `models.GetLimitMsg` 函数，用于获取指定聊天室的消息列表。它接收聊天室ID `roomId` 和偏移量 `offset` 作为参数，并返回一个包含消息信息的 `[]map[string]interface{}` 切片。具体实现细节需要查看 `models.GetLimitMsg` 函数。

2. `GetLimitPrivateMsg(uid, toUId string, offset int) []map[string]interface{}`：该函数调用了 `models.GetLimitPrivateMsg` 函数，用于获取两个用户之间的私聊消息列表。它接收发送消息的用户ID `uid`、接收消息的用户ID `toUId` 和偏移量 `offset` 作为参数，并返回一个包含消息信息的 `[]map[string]interface{}` 切片。具体实现细节需要查看 `models.GetLimitPrivateMsg` 函数。

这些函数的目的是封装 `models` 包中的函数，使其在消息服务中更容易使用和调用。它们提供了一种简化的方式来获取特定条件下的消息列表。