让我们逐行分析`session`包中的每一句代码：

1. `EnableCookieSession`函数：
```go
func EnableCookieSession() gin.HandlerFunc {
	store := cookie.NewStore([]byte(viper.GetString(`app.cookie_key`)))
	return sessions.Sessions("go-gin-chat", store)
}
```
该函数返回一个`gin.HandlerFunc`类型的中间件函数，用于启用Cookie会话。它首先创建一个基于Cookie的存储对象`store`，使用配置文件中的`app.cookie_key`作为密钥。然后，使用`sessions.Sessions`函数创建并返回一个会话中间件。

2. `SaveAuthSession`函数：
```go
func SaveAuthSession(c *gin.Context, info interface{}) {
	session := sessions.Default(c)
	session.Set("uid", info)
	session.Save()
}
```
该函数用于保存认证会话信息。它首先通过`sessions.Default`函数获取当前会话对象`session`。然后，使用`session.Set`方法将用户ID（`info`参数）保存到会话中的"uid"键。最后，调用`session.Save`方法将会话信息保存到存储中。

3. `GetSessionUserInfo`函数：
```go
func GetSessionUserInfo(c *gin.Context) map[string]interface{} {
	session := sessions.Default(c)

	uid := session.Get("uid")

	data := make(map[string]interface{})
	if uid != nil {
		user := models.FindUserByField("id", uid.(string))
		data["uid"] = user.ID
		data["username"] = user.Username
		data["avatar_id"] = user.AvatarId
	}
	return data
}
```
该函数用于获取会话中保存的用户信息。它首先通过`sessions.Default`函数获取当前会话对象`session`。然后，使用`session.Get`方法获取会话中的"uid"键对应的值。如果"uid"存在，表示用户已登录，根据用户ID查询数据库中的用户信息，并将用户ID、用户名和头像ID保存到`data`字典中。最后，返回包含用户信息的`data`字典。

4. `ClearAuthSession`函数：
```go
func ClearAuthSession(c *gin.Context) {
	session := sessions.Default(c)
	session.Clear()
	session.Save()
}
```
该函数用于清除会话信息，实现用户注销功能。它首先通过`sessions.Default`函数获取当前会话对象`session`。然后，调用`session.Clear`方法清除会话中的所有键值对。最后，调用`session.Save`方法将清除后的会话信息保存到存储中。

5. `HasSession`函数：
```go
func HasSession(c *gin.Context) bool {
	session := sessions.Default(c)
	if sessionValue := session.Get("uid"); sessionValue == nil {
		return false
	}
	return true
}
```
该函数用于检查当前会话是否存在。它首先通过`sessions.Default`函数获取当前会话对象`session`。然后，使用`session.Get`方法获取会话中的"uid"键对应的值。如果"uid"为`nil`，表示会话不存在，返回`false`；否则，返回`true`表示会话存在。

6. `AuthSessionMiddle`函数：
```go
func AuthSessionMiddle() gin.HandlerFunc {


	return func(c *gin.Context) {
		session := sessions.Default(c)
		sessionValue := session.Get("uid")
		if sessionValue == nil {
			c.Redirect(http.StatusFound, "/")
			return
		}

		uidInt, _ := strconv.Atoi(sessionValue.(string))

		if uidInt <= 0 {
			c.Redirect(http.StatusFound, "/")
			return
		}

		c.Set("uid", sessionValue)

		c.Next()
		return
	}
}
```
该函数返回一个`gin.HandlerFunc`类型的中间件函数，用于验证会话是否存在以及会话中保存的用户ID是否有效。它首先通过`sessions.Default`函数获取当前会话对象`session`。然后，使用`session.Get`方法获取会话中的"uid"键对应的值。如果"uid"为`nil`，表示会话不存在或用户未登录，通过重定向将用户导航回首页。接着，将会话中的"uid"转换为整数类型，并检查其是否小于等于0，若是，则认为用户ID无效，同样通过重定向将用户导航回首页。最后，使用`c.Set`方法设置上下文中的"uid"变量为会话中的用户ID，并调用`c.Next`方法继续处理后续请求。