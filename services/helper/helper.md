<!--
 * @Author: yowayimono
 * @Date: 2023-06-11 18:10:59
 * @LastEditors: yowayimono
 * @LastEditTime: 2023-06-11 18:11:05
 * @Description: nothing
-->
下面是对 `helper` 包中代码的逐行注释：

```go
package helper

import (
	"crypto/md5"
	"encoding/hex"
	"unicode/utf8"
)
```

导入了 `crypto/md5`、`encoding/hex` 和 `unicode/utf8` 包。

```go
func InArray(needle interface{}, hystack interface{}) bool {
	switch key := needle.(type) {
	case string:
		for _, item := range hystack.([]string) {
			if key == item {
				return true
			}
		}
	case int:
		for _, item := range hystack.([]int) {
			if key == item {
				return true
			}
		}
	case int64:
		for _, item := range hystack.([]int64) {
			if key == item {
				return true
			}
		}
	default:
		return false
	}
	return false
}
```

定义了一个函数 `InArray`，用于判断给定的 `needle` 是否存在于 `hystack` 中。该函数接受两个参数，`needle` 和 `hystack`，分别表示要查找的值和要搜索的数组或切片。函数通过类型断言判断 `needle` 的类型，并根据类型执行相应的逻辑进行搜索，支持 `string`、`int` 和 `int64` 类型的值。如果找到了匹配项，则返回 `true`，否则返回 `false`。

```go
func Md5Encrypt(s string) string {
	m := md5.New()
	m.Write([]byte(s))
	return hex.EncodeToString(m.Sum(nil))
}
```

定义了一个函数 `Md5Encrypt`，用于对给定的字符串 `s` 进行 MD5 加密。函数内部创建了一个 MD5 实例，并将字符串转换为字节数组后写入该实例。然后通过 `hex.EncodeToString` 将加密后的结果转换为十六进制字符串并返回。

```go
func MbStrLen(str string) int {
	return utf8.RuneCountInString(str)
}
```

定义了一个函数 `MbStrLen`，用于计算给定字符串 `str` 的字符数（以字符为单位）。函数使用 `utf8.RuneCountInString` 函数来获取字符串中的 Unicode 字符数量，并将其作为结果返回。

综上所述，该代码定义了一些常用的辅助函数，包括判断值是否存在于数组中的 `InArray` 函数，对字符串进行 MD5 加密的 `Md5Encrypt` 函数，以及计算字符串字符数的 `MbStrLen` 函数。这些函数提供了在应用程序中常用的功能。