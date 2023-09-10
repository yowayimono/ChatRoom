<!--
 * @Author: yowayimono
 * @Date: 2023-06-12 13:43:51
 * @LastEditors: yowayimono
 * @LastEditTime: 2023-06-12 13:44:45
 * @Description: nothing
-->
以下是对代码的逐行中文注释和详细说明：

```go
package controller

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"github.com/spf13/viper"
	"go-gin-chat/services/img_upload_connector"
	"net/http"
	"os"
)
```

首先，我们引入了所需的包。

```go
func ImgKrUpload(c *gin.Context) {
```

定义了一个名为 `ImgKrUpload` 的函数，该函数用于处理图片上传请求。

```go
	file, err := c.FormFile("file")
	if err != nil {
		c.String(http.StatusBadRequest, fmt.Sprintf("get form err: %s", err.Error()))
		return
	}
```

从请求中获取上传的文件。使用 `c.FormFile` 函数，参数为表单中文件的字段名 `"file"`。如果获取文件失败，则返回错误信息。

```go
	filepath := viper.GetString(`app.upload_file_path`)

	if _, err := os.Stat(filepath); err != nil {
		if !os.IsExist(err) {
			os.MkdirAll(filepath, os.ModePerm)
		}
	}
```

从配置文件中读取上传文件的存储路径。然后，检查路径是否存在。如果路径不存在，则创建该路径。

```go
	filename := filepath + file.Filename

	if err := c.SaveUploadedFile(file, filename); err != nil {
		c.String(http.StatusBadRequest, fmt.Sprintf("upload file err: %s", err.Error()))
		return
	}
```

将上传的文件保存到指定路径中。使用 `c.SaveUploadedFile` 函数，参数为上传的文件和保存的完整文件路径。如果保存文件失败，则返回错误信息。

```go
	krUpload := img_upload_connector.ImgCreate().Upload(filename)
```

使用图像上传连接器的 `ImgCreate` 函数创建连接器实例，并调用 `Upload` 方法将文件上传。返回的 `krUpload` 是上传后的图片URL。

```go
	os.Remove(filename)
```

删除临时文件，因为已经上传成功并保存了文件。

```go
	c.JSON(http.StatusOK, gin.H{
		"code": 0,
		"data": map[string]interface{}{
			"url": krUpload,
		},
	})
```

返回 JSON 响应，包含上传成功的信息，其中 `url` 字段是上传后的图片URL。

以上是代码的详细注释说明。该函数主要处理文件上传的请求，包括获取上传的文件、保存文件、上传文件到指定位置，并返回上传成功后的图片URL。

## 图片上传流程
上传图片的流程通常是从用户本地将图片上传到服务器。服务器接收到用户上传的图片后，会先将其保存到本地存储位置。然后，服务器可以对图片进行处理（如压缩、裁剪等），之后将处理后的图片上传到前端展示的位置（如存储在云存储服务或CDN上）。最后，服务器会将用户上传的原始图片从本地删除，以节省存储空间。

这样做的好处是，用户上传的图片经过服务器处理和存储后，可以提供给前端进行展示和访问，同时避免了在每次访问时都需要处理图片的性能损耗。此外，删除用户上传的原始图片可以保护用户隐私和节约存储空间。

需要注意的是，图片的删除可以在适当的时机进行，例如在确认图片已经成功上传到前端展示位置且不再需要原始图片时。同时，确保在删除图片时处理错误情况和异常情况，以保证系统的稳定性和可靠性。


上面给出的代码是一个处理图片上传的示例函数`ImgKrUpload`，下面是逐行注释详细讲解该函数的上传图片流程：

```go
func ImgKrUpload(c *gin.Context) {
    // 获取上传的文件
    file, err := c.FormFile("file")
    if err != nil {
        // 如果获取文件出错，则返回错误信息
        c.String(http.StatusBadRequest, fmt.Sprintf("get form err: %s", err.Error()))
        return
    }
    
    // 获取文件保存路径
    filepath := viper.GetString(`app.upload_file_path`)
    
    // 如果文件保存路径不存在，则创建目录
    if _, err := os.Stat(filepath); err != nil {
        if !os.IsExist(err) {
            os.MkdirAll(filepath, os.ModePerm)
        }
    }
    
    // 拼接文件保存的完整路径
    filename := filepath + file.Filename
    
    // 将上传的文件保存到服务器指定路径
    if err := c.SaveUploadedFile(file, filename); err != nil {
        // 如果保存文件出错，则返回错误信息
        c.String(http.StatusBadRequest, fmt.Sprintf("upload file err: %s", err.Error()))
        return
    }
    
    // 调用图片上传连接器将保存的文件上传到指定位置
    krUpload := img_upload_connector.ImgCreate().Upload(filename)
    
    // 删除临时保存的图片文件
    os.Remove(filename)
    
    // 返回上传成功的响应
    c.JSON(http.StatusOK, gin.H{
        "code": 0,
        "data": map[string]interface{}{
            "url": krUpload,
        },
    })
}
```

该函数使用了`gin`框架来处理HTTP请求。具体的上传图片流程如下：

1. 通过`c.FormFile("file")`获取用户上传的文件。
2. 根据配置文件中的设置，获取文件保存的路径。
3. 如果文件保存路径不存在，则创建目录。
4. 构建完整的文件保存路径，将上传的文件保存到服务器指定的路径中，使用`c.SaveUploadedFile(file, filename)`实现。
5. 调用图片上传连接器（`img_upload_connector.ImgCreate().Upload(filename)`）将保存的文件上传到指定位置，这一部分代码没有给出具体实现，需要根据具体的图片上传服务或处理方式进行编写。
6. 删除临时保存的图片文件，使用`os.Remove(filename)`。
7. 返回上传成功的响应，包括图片的URL等信息。

请注意，这只是一个简化的示例函数，实际的图片上传流程可能还需要处理错误情况、文件重命名、文件大小限制、文件类型验证等等。具体的实现方式可能因具体的应用需求和架构而有所不同。