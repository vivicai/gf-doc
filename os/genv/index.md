
# genv

环境变量管理模块。

使用方式：
```go
import "gitee.com/johng/gf/g/os/genv"
```

方法列表：[godoc.org/github.com/johng-cn/gf/g/os/genv](https://godoc.org/github.com/johng-cn/gf/g/os/genv)
```go
func All() []string
func Get(k string, def ...string) string
func Remove(k string) error
func Set(k, v string) error
```

