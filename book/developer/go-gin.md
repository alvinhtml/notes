# Gin 框架

## 获取参数

### 获取 URI 参数

```go
// https://github.com/gin-gonic/gin/pull/1612
type ById struct {
	Id uint `json:"id" form:"id" uri:"id"`
}

func GetUser(c *gin.Context) {
	var byId request.ById
	c.ShouldBindUri(&byId)

	fmt.Println(byId)
}
```
