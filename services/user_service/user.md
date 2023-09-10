
```go
func Login(c *gin.Context) {
	// 从请求中获取用户名、密码和头像ID
	username := c.PostForm("username")
	pwd := c.PostForm("password")
	avatarId := c.PostForm("avatar_id")

	var u validator.User
	// 创建一个验证用户数据的结构体对象
	u.Username = username
	u.Password = pwd
	u.AvatarId = avatarId

	// 使用 ShouldBind 函数将请求中的数据绑定到结构体对象，检查是否发生错误
	if err := c.ShouldBind(&u); err != nil {
		c.JSON(http.StatusOK, gin.H{"code": 5000, "msg": err.Error()})
		return
	}

	// 根据用户名查找用户信息
	user := models.FindUserByField("username", username)
	// 将用户信息赋值给 userInfo
	userInfo := user
	// 对密码进行 MD5 加密
	md5Pwd := helper.Md5Encrypt(pwd)

	if userInfo.ID > 0 {
		// 用户存在，验证密码是否正确
		if userInfo.Password != md5Pwd {
			c.JSON(http.StatusOK, gin.H{
				"code": 5000,
				"msg":  "密码错误",
			})
			return
		}

		// 更新用户的头像ID
		models.SaveAvatarId(avatarId, user)
	} else {
		// 用户不存在，创建新用户
		userInfo = models.AddUser(map[string]interface{}{
			"username":  username,
			"password":  md5Pwd,
			"avatar_id": avatarId,
		})
	}

	if userInfo.ID > 0 {
		// 用户信息有效，保存用户的认证会话，并返回成功的 JSON 响应
		session.SaveAuthSession(c, string(strconv.Itoa(int(userInfo.ID))))
		c.JSON(http.StatusOK, gin.H{
			"code": 0,
		})
		return
	} else {
		// 用户信息无效，返回系统错误的 JSON 响应
		c.JSON(http.StatusOK, gin.H{
			"code": 5001,
			"msg":  "系统错误",
		})
		return
	}
}

func GetUserInfo(c *gin.Context) map[string]interface{} {
	// 从会话中获取用户信息
	return session.GetSessionUserInfo(c)
}

func Logout(c *gin.Context) {
	// 清除用户的认证会话，并重定向到主页
	session.ClearAuthSession(c)
	c.Redirect(http.StatusFound, "/")
	return
}
```

以上是 `user_service` 包中代码的逐行分析和加上注释的解释。
这段代码是一个用户服务包，提供了一些与用户相关的函数。

1. `Login(c *gin.Context)`: 该函数处理用户登录操作。它从请求中获取用户名、密码和头像ID，并进行验证和处理。首先，它使用 `models.FindUserByField` 函数根据用户名查找用户信息。然后，它对比输入的密码和数据库中存储的密码进行验证。如果密码匹配，则调用 `models.SaveAvatarId` 函数更新用户的头像ID。如果用户不存在，则调用 `models.AddUser` 函数创建新用户。最后，如果用户验证成功，将用户ID保存到会话中，并返回相应的 JSON 响应。

2. `GetUserInfo(c *gin.Context) map[string]interface{}`: 该函数从会话中获取当前用户的信息并返回一个包含用户信息的 `map[string]interface{}`。它调用了 `session.GetSessionUserInfo` 函数来实现这一功能。

3. `Logout(c *gin.Context)`: 该函数处理用户登出操作。它调用 `session.ClearAuthSession` 函数清除当前会话中的用户信息，并重定向用户到首页。

这些函数用于处理用户登录、获取用户信息和用户登出的逻辑。它们依赖于其他包（如 `models`、`helper` 和 `session`）来完成具体的操作和功能。