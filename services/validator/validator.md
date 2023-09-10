<!--
 * @Author: yowayimono
 * @Date: 2023-06-11 18:00:10
 * @LastEditors: yowayimono
 * @LastEditTime: 2023-06-11 18:00:21
 * @Description: nothing
-->


```go
type User struct {
	Username  string `binding:"required,max=16,min=2"`  // 用户名字段，要求必填，最大长度为16，最小长度为2
	Password  string `binding:"required,max=32,min=6"`  // 密码字段，要求必填，最大长度为32，最小长度为6
	AvatarId  string `binding:"required,numeric"`       // 头像ID字段，要求必填且为数字类型
}
```

以上代码定义了一个 `User` 结构体，该结构体用于验证用户数据。结构体的字段包括 `Username`、`Password` 和 `AvatarId`，每个字段都有相应的验证标签，用于指定验证规则。注释部分对每个字段的验证规则进行了解释，例如 `Username` 字段要求必填、最大长度为16、最小长度为2，`Password` 字段要求必填、最大长度为32、最小长度为6，`AvatarId` 字段要求必填且为数字类型。这些验证规则可以在后续的表单绑定过程中使用，以确保用户提交的数据满足指定的验证条件。