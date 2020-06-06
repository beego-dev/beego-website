# Cache

cache 模块使用来做数据缓存，目前支持四种模式 file、memcache、memory 和 redis 四种引擎。

## 基础入门
在使用 beego 的 Cache 的时候，第一件事就是要引入 cache 包：
```go
import (
	"github.com/astaxie/beego/cache"
)
```

引入包之后，就要创建一个新的全局变量对象：
```go
bm, err := cache.NewCache("memory", `{"interval":60}`)
```

这里注意 NewCache 这个方法，第一个参数是用来声明我们要使用那种 cache 引擎，上面说过一共有四种可以选择。第二个参数就是对应引擎的配置，

## 具体使用
### Memory
```go
import (
	"time"

	"github.com/astaxie/beego/cache"
	"github.com/astaxie/beego/logs"
)

func main() {
	// create memory
	bm, err := cache.NewCache("memory", `{"interval":60}`)
	if err != nil {
		logs.Error(err)
	}

	// put
	isPut := bm.Put("name", "beego", time.Second*10)
  logs.Info(isPut)

  // get
	result := bm.Get("name")
	logs.Info(result)
}
```

### file
```go
import (
	"time"

	"github.com/astaxie/beego/cache"
	"github.com/astaxie/beego/logs"
)

func main() {
	bm, err := cache.NewCache("file", `{"CachePath":"./cache","FileSuffix":".cache", "EmbedExpiry": "120"}`)
	if err != nil {
		logs.Error(err)
	}

	// put
	isPut := bm.Put("name", "beego", time.Second*10)
	logs.Info(isPut)

	// get
	result := bm.Get("name")
	logs.Info(result)
}
```

### redis
```go
import (
	"time"

	"github.com/astaxie/beego/cache"
	_ "github.com/astaxie/beego/cache/redis"
	"github.com/astaxie/beego/logs"
)

func main() {
	// create cache
	bm, err := cache.NewCache("redis", `{"key":"default", "conn":":6379", "password":"123456", "dbNum":"0"}`)
	if err != nil {
		logs.Error(err)
	}

	// put
	isPut := bm.Put("astaxie", 1, time.Second*10)
	logs.Info(isPut)

	// get
	result := bm.Get("astaxie")
	logs.Info(string(result.([]byte)))
}
```

### memcache
```go
import (
	"time"

	"github.com/astaxie/beego/cache"
	_ "github.com/astaxie/beego/cache/memcache"
	"github.com/astaxie/beego/logs"
)

func main() {
	// create memory
	bm, err := cache.NewCache("memcache", `{"conn":"127.0.0.1:11211"}`)
	if err != nil {
		logs.Error(err)
	}

	// put
	isPut := bm.Put("name", "beego", time.Second*10)
	logs.Info(isPut)

	// get
	result := bm.Get("name")
	logs.Info(result)
}
```

## 引用
示例代码地址：[https://github.com/beego-dev/beego-example/tree/master/cache](https://github.com/beego-dev/beego-example/tree/master/cache)