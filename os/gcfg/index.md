[TOC]


gf的配置管理由```gcfg```模块实现，gcfg包是并发安全的，仅提供配置文件读取功能，不提供数据写入/修改功能，**支持的数据文件格式包括： `JSON`、`XML`、`YAML/YML`、`TOML`**，项目中开发者可以灵活地选择自己熟悉的配置文件格式来进行配置管理。如果想要支持对数据文件的读取及修改，并且可以进行数据格式转换，请参看[gparser包](encoding/gparser/index.md)。

使用方式：
```go
import "gitee.com/johng/gf/g/os/gcfg"
```

# 方法列表 

[godoc.org/github.com/johng-cn/gf/g/os/gcfg](https://godoc.org/github.com/johng-cn/gf/g/os/gcfg)

```go
type Config
    func New(path string, file ...string) *Config
    func (c *Config) AddPath(path string) error
    func (c *Config) Get(pattern string, file ...string) interface{}
    func (c *Config) GetArray(pattern string, file ...string) []interface{}
    func (c *Config) GetBool(pattern string, file ...string) bool
    func (c *Config) GetFilePath(file ...string) string
    func (c *Config) GetFloat32(pattern string, file ...string) float32
    func (c *Config) GetFloat64(pattern string, file ...string) float64
    func (c *Config) GetFloats(pattern string, file ...string) []float64
    func (c *Config) GetInt(pattern string, file ...string) int
    func (c *Config) GetInt16(pattern string, file ...string) int16
    func (c *Config) GetInt32(pattern string, file ...string) int32
    func (c *Config) GetInt64(pattern string, file ...string) int64
    func (c *Config) GetInt8(pattern string, file ...string) int8
    func (c *Config) GetInterfaces(pattern string, file ...string) []interface{}
    func (c *Config) GetInts(pattern string, file ...string) []int
    func (c *Config) GetMap(pattern string, file ...string) map[string]interface{}
    func (c *Config) GetString(pattern string, file ...string) string
    func (c *Config) GetStrings(pattern string, file ...string) []string
    func (c *Config) GetToStruct(pattern string, objPointer interface{}, file ...string) error
    func (c *Config) GetUint(pattern string, file ...string) uint
    func (c *Config) GetUint16(pattern string, file ...string) uint16
    func (c *Config) GetUint32(pattern string, file ...string) uint32
    func (c *Config) GetUint64(pattern string, file ...string) uint64
    func (c *Config) GetUint8(pattern string, file ...string) uint8
    func (c *Config) Reload()
    func (c *Config) SetFileName(name string)
    func (c *Config) SetPath(path string) error
    func (c *Config) SetViolenceCheck(check bool)
```

`gcfg`包最大的特点是支持按层级获取配置数据，层级访问通过英文"`.`"号指定，其中```pattern```参数和[gjson包](encoding/gjson/index.md)的`pattern`参数一致。参数`file`指定需要读取的配置文件目录下的文件名称，是一个可选参数，默认为`config.toml`（即读取`config.toml`配置文件数据）。`toml`类型文件也是推荐的配置文件格式。

# 单例管理

`gcfg`集成到了gf框架的单例管理器中，可以方便地通过`g.Config()`获取默认的全局配置管理对象，默认是读取 **当前执行目录（执行文件所在目录）** 下的```config.toml```文件。

# 使用示例

我们有两种方式进行配置文件的管理，使用全局的```g.Config()```获取单例对象（推荐），或者单独使用```gcfg```模块进行管理（使用请参考【[其他用法](os/gcfg/other.md)】章节）。

我们来一个看示例，演示如何读取全局配置的信息。需要注意的是，全局配置是与框架相关的，因此统一使用```g.Config()```进行获取。以下是一个默认的全局配置文件，包含了模板引擎的目录配置以及MySQL数据库集群(两台master)的配置。

配置文件如下：
```toml
# 模板引擎目录
viewpath = "/home/www/templates/"
# MySQL数据库配置
[database]
    [[database.default]]
        host     = "127.0.0.1"
        port     = "3306"
        user     = "root"
        pass     = "123456"
        name     = "test"
        type     = "mysql"
        role     = "master"
        charset  = "utf8"
        priority = "1"
    [[database.default]]
        host     = "127.0.0.1"
        port     = "3306"
        user     = "root"
        pass     = "123456"
        name     = "test"
        type     = "mysql"
        role     = "master"
        charset  = "utf8"
        priority = "1"
# Redis数据库配置
[redis]
    disk  = "127.0.0.1:6379,0"
    cache = "127.0.0.1:6379,1"
```
注意：以上toml配置文件中的密码字段值```123456```使用了双引号进行包含，用以标识该密码字段为字符串类型，防止配置文件读取时自动转换为整型，防止引起歧义。

示例程序如下：
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g"
)

func main() {
    fmt.Println(g.Config().GetString("viewpath"))
    fmt.Println(g.Config().GetString("database.default.0.host"))
}
```
以上示例为读取数据库的第一个配置的host信息。运行后输出：
```html
/home/www/templates/
127.0.0.1
```
可以看到，我们可以通过```g.Config()```方法获取一个全局的配置管理器单例对象。配置文件内容可以通过英文“```.```”号进行层级访问（数组默认从0开始），`pattern`参数```database.default.0.host```表示读取database配置项中default数据库集群中的第0项数据库服务器的host数据。


# 修改配置目录

gf框架的配置管理器支持两种方式的配置目录修改。

## 修改配置目录
配置管理器作为gf框架的核心组件，可以通过以下方式修改配置管理器的默认配置文件查找目录：
1. (推荐)单例模式获取全局```g.Config()```对象，通过`SetPath`方法手动修改；
2. 修改命令行启动参数 - ```gf.cfgpath```；
3. 修改指定的环境变量 - ```gf.cfgpath```；

例如，我们的执行程序文件为```main```，那么可以通过以下方式修改配置管理器的配置文件目录(Linux下)：

1. **(推荐)通过单例模式**
	```go
    g.Config().SetPath("/opt/config")
    ```
3. **通过命令行参数**
    ```shell
    ./main --gf.cfgpath=/opt/config/
    ```
1. **通过环境变量**
    * 启动时修改环境变量：
        ```shell
        gf.cfgpath=/opt/config/; ./main
        ```
    * 使用genv包来修改环境变量：
        ```go
        genv.Set("gf.cfgpath", "/opt/config")
        ```

## 添加搜索目录

`gf`框架的配置管理器支持非常灵活的多目录自动搜索功能，通过```SetPath```可以修改目录管理目录为**唯一**的一个目录地址，同时，我们推荐通过```AddPath```方法添加多个搜索目录，配置管理器底层将会按照添加目录的顺序作为优先级进行自动检索。直到检索到一个匹配的文件路径为止，如果在所有搜索目录下查找不到配置文件，那么会返回失败。

当我们使用对象管理器```g.Config()```获取配置管理单例对象时，框架会自动添加两个配置文件搜索目录：
* 当前可执行文件的目录;
* 源代码`main`模块文件目录(仅对源码开发环境有效);

# 自动检测更新

配置管理器使用了缓存机制，当配置文件第一次被读取后会被缓存到内存中，下一次读取时将会直接从缓存中获取，以提高性能。同时，配置管理器提供了对配置文件的**自动检测更新机制**，当配置文件在外部被修改后，配置管理器能够即时地刷新配置文件的缓存内容。

配置管理器的自动检测更新机制是`gf`框架特有的一大特色。




