
[TOC]


# 全局对象

`gf`框架封装了一些常用的数据类型以及对象，可以直接通过```g.*```方便获取。

使用方式：
```go
import "gitee.com/johng/gf/g"
```

## 数据类型
```go
// 常用map数据结构
type Map map[string]interface{}

// 常用list数据结构
type List []Map
```

## 常用对象

1. **(单例)配置管理对象**
	```go
    // 参数file未非必需参数，表示指定获取的配置文件管理对象
    func Config(file...string) *gcfg.Config
    ```
3. **(单例)模板引擎对象**
	```go
    func View() *gview.View
    ```
5. **(单例)Http Server**
	```go
    func Server(name...interface{}) *ghttp.Server
    ```
7. **(单例)TCP Server**
	```go
    func TcpServer(name...interface{}) *gtcp.Server
    ```
9. **(单例)Udp Server**
	```go
    func UdpServer(name...interface{}) *gudp.Server
    ```
11. **(单例)数据库ORM对象**
	```go
    func Database(name...string) *gdb.Db
    // 别名
    func DB(name...string) *gdb.Db
    ```
13. **(单例)Redis客户端对象**
	```go
    func Redis(name...string) *gredis.Redis
    ```

