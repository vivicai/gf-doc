框架源码目录介绍：

```html
.
├── g			        框架源码目录
│   ├── container           并发安全容器
│   │   ├── garray          并发安全数组
│   │   ├── gchan           优雅的chan操作
│   │   ├── glist           并发安全链表
│   │   ├── gmap            并发安全Map
│   │   ├── gpool           对象复用池
│   │   ├── gqueue          并发安全队列
│   │   ├── gring           并发安全的环
│   │   ├── gset            并发安全集合
│   │   ├── gtype           并发安全类型
│   │   └── gvar            通用动态变量
│   ├── crypto          加密/解密模块
│   │   ├── gcrc32          CRC32
│   │   ├── gaes            AES
│   │   ├── gdes            DES
│   │   ├── gmd5            MD5
│   │   └── gsha1           SHA1
│   ├── database        数据库管理
│   │   ├── gdb             通用数据库管理
│   │   ├── gkafka          Kafka客户端
│   │   └── gredis          Redis客户端
│   ├── encoding        编码/解码模块
│   │   ├── gbase64         BASE64
│   │   ├── gbinary         二进制操作
│   │   ├── gcharset        字符编码转换
│   │   ├── gcompress       压缩/解压
│   │   ├── ghash           哈希函数
│   │   ├── ghtml           HTML编码/解析
│   │   ├── gjson           JSON
│   │   ├── gparser         通用数据编码/解析
│   │   ├── gtoml           TOML
│   │   ├── gurl            URL
│   │   ├── gxml            XML
│   │   └── gyaml           YAML
│   ├── frame           常用框架管理
│   │   ├── gins            单例管理
│   │   └── gmvc            MVC开发模式
│   ├── net             网络通信管理
│   │   ├── ghttp           HTTP组件
│   │   ├── gipv4           IPV4
│   │   ├── gipv6           IPV6
│   │   ├── gscanner        局域网扫描
│   │   ├── gsmtp           SMTP
│   │   ├── gtcp            TCP组件
│   │   └── gudp            UDP组件
│   ├── os              系统管理模块
│   │   ├── gcache          单进程高速缓存
│   │   ├── gcfg            配置文件管理器
│   │   ├── gcmd            命令行参数管理
│   │   ├── genv            环境变量
│   │   ├── gfcache         文件缓存
│   │   ├── gfile           文件管理
│   │   ├── gfilepool       文件指针池
│   │   ├── gflock          文件锁
│   │   ├── gfsnotify       文件监控
│   │   ├── glog            日志管理
│   │   ├── gmlock          内存锁
│   │   ├── gmmap           MMAP
│   │   ├── gproc           进程管理通信
│   │   ├── grpool          协程池
│   │   ├── gtime           时间日期
│   │   └── gview           模板引擎
│   └── util            常用工具模块
│       ├── gconv           基本类型转换
│       ├── gpage           分页管理
│       ├── grand           随机数管理
│       ├── gregx           正则表达式
│       ├── gutil           其他工具方法
│       └── gvalid          表单/数据校验
├── geg                 框架示例代码
├── vendor              第三方依赖包
└── version.go          当前框架版本
```
