# gtoml

TOML数据格式编码解析。

使用方式：
```go
import "gitee.com/johng/gf/g/encoding/gtoml"
```

方法列表：
```go
func Decode(v []byte) (interface{}, error)
func DecodeTo(v []byte, result interface{}) error
func Encode(v interface{}) ([]byte, error)
func ToJson(v []byte) ([]byte, error)
```