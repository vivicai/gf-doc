```gtcp```包提供了连接池的特性，由```gtcp.PoolConn```对象实现，连接池缓存固定时间为600秒，且内部实现了数据发送时的断开重连机制。连接池非常适合于频繁的短链接操作且连接并发量大的场景。我们接下来使用两个示例来演示一下连接池的作用。

# 示例1，基本使用
```go
package main

import (
    "fmt"
    "time"
    "gitee.com/johng/gf/g/net/gtcp"
    "gitee.com/johng/gf/g/os/glog"
    "gitee.com/johng/gf/g/os/gtime"
)

func main() {
    // Server
    go gtcp.NewServer("127.0.0.1:8999", func(conn *gtcp.Conn) {
        defer conn.Close()
        for {
            data, err := conn.Receive(-1)
            if len(data) > 0 {
                if err := conn.Send(append([]byte("> "), data...)); err != nil {
                    fmt.Println(err)
                }
            }
            if err != nil {
                break
            }
        }
    }).Run()

    time.Sleep(time.Second)

    // Client
    for {
       if conn, err := gtcp.NewPoolConn("127.0.0.1:8999"); err == nil {
           if b, err := conn.SendReceive([]byte(gtime.Datetime()), -1); err == nil {
               fmt.Println(string(b), conn.LocalAddr(), conn.RemoteAddr())
           } else {
               fmt.Println(err)
           }
           conn.Close()
       } else {
           glog.Error(err)
       }
       time.Sleep(time.Second)
    }
}
```
在这个示例中，我们将Server和Client写到一个文件中，其中Server创建新的goroutine异步运行，Client在main goroutine中执行。Server端是一个回显服务器，Client每隔1秒向Server端发送当前的时间，经过Server端回显返回后，在Client端打印出双方的连接端口信息。
执行后，结果如下：
```html
> 2018-07-11 23:29:54 127.0.0.1:55876 127.0.0.1:8999
> 2018-07-11 23:29:55 127.0.0.1:55876 127.0.0.1:8999
> 2018-07-11 23:29:56 127.0.0.1:55876 127.0.0.1:8999
> 2018-07-11 23:29:57 127.0.0.1:55876 127.0.0.1:8999
> 2018-07-11 23:29:58 127.0.0.1:55876 127.0.0.1:8999
...
```
可以看到，Client的端口一直未变，每一次通过```gtcp.NewConn("127.0.0.1:8999")```获得的都是同一个```gtcp.Conn```对象，且每一次```conn.Close()```时并不是真正的关闭连接，而是将该对象重新丢回到连接池里循环使用。



# 示例2，连接断开情况
这个例子是为了展示当服务端关闭连接后，该连接对象还是否有效的处理。

```go
package main

import (
    "fmt"
    "time"
    "gitee.com/johng/gf/g/net/gtcp"
    "gitee.com/johng/gf/g/os/glog"
    "gitee.com/johng/gf/g/os/gtime"
)

func main() {
    // Server
    go gtcp.NewServer("127.0.0.1:8999", func(conn *gtcp.Conn) {
        defer conn.Close()
        for {
            data, err := conn.Receive(-1)
            if len(data) > 0 {
                if err := conn.Send(append([]byte("> "), data...)); err != nil {
                    fmt.Println(err)
                }
            }
            if err != nil {
                break
            }
            return
        }
    }).Run()

    time.Sleep(time.Second)

    // Client
    for {
       if conn, err := gtcp.NewPoolConn("127.0.0.1:8999"); err == nil {
           if b, err := conn.SendReceive([]byte(gtime.Datetime()), -1); err == nil {
               fmt.Println(string(b), conn.LocalAddr(), conn.RemoteAddr())
           } else {
               fmt.Println(err)
           }
           conn.Close()
       } else {
           glog.Error(err)
       }
       time.Sleep(time.Second)
    }
}
```
执行后，输出结果如下：
```html
> 2018-07-20 12:56:15 127.0.0.1:59368 127.0.0.1:8999
EOF
> 2018-07-20 12:56:17 127.0.0.1:59376 127.0.0.1:8999
EOF
> 2018-07-20 12:56:19 127.0.0.1:59378 127.0.0.1:8999
EOF
...
```
在这个示例中，Server每处理完毕一条请求之后便关闭链接。Client在第一条请求发送完毕后，由于连接池的IO复用特性，下一次获取到的将是同一个连接对象，由于Server链接已关闭，第二次请求写入成功（其实并未真正成功，需要通过下一次的读取操作才能检测到链接错误），但是读取却失败了（```EOF```表示目标连接关闭），因此这个时候Client执行```Close```时将会销毁该连接操作对象，而不是进一步复用。下一次再通过```gtcp.NewPoolConn```获得连接对象时，Client将会与Server创建一个新的连接进行数据通信。所以你看到Client的端口一直在变化，那是因为该```gtcp.Conn```对象已经是一个新的连接对象，之前的连接对象已经被销毁。

因此，在使用gtcp连接池特性时，需要注意当错误产生时的对象重建机制。

