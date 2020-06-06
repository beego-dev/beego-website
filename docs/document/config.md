# Config
beego 提供了可以用来解析配置文件的工具库，可以用来解析配置文件当中的内容，目前支持四种文件格式：ini、json、xml、yaml。并且支持自动加载配置文件的功能。

## 自动加载
如果使用自动加载，beego 会去 conf 文件夹下寻找配置文件自动加载。
```go
package main

import (
	"github.com/astaxie/beego"
	_ "github.com/astaxie/beego/config"
	"github.com/astaxie/beego/logs"
)

func main() {
	logs.Info("auto load config name is",beego.AppConfig.String("name"))
}
```

## 手动加载
```go
import (
	"github.com/astaxie/beego"
	"github.com/astaxie/beego/logs"
)

var (
	ConfigFile = "./app.conf"
)

func main() {
	err := beego.LoadAppConfig("ini", ConfigFile)
	if err != nil {
		logs.Critical("An error occurred:", err)
		panic(err)
	}
	logs.Info("load config name is",beego.AppConfig.String("name"))
}
```

## 引用
示例代码地址：[https://github.com/beego-dev/beego-example/tree/master/config](https://github.com/beego-dev/beego-example/tree/master/config)
