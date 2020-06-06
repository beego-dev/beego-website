# Logs
Beego 提供了一个通用的日志处理模块，目前支持的引擎有 file、console、net、smtp。

## Console
```go
import "github.com/astaxie/beego/logs"

func main() {
	logs.Info("hello beego")
}
```

## File
```go
import "github.com/astaxie/beego/logs"

func main() {
	err := logs.SetLogger(logs.AdapterFile, `{"filename":"project.log","level":7,"maxlines":0,"maxsize":0,"daily":true,"maxdays":10,"color":true}`)
	if err != nil {
		panic(err)
	}
	logs.Info("hello beego")
}
```

## 引用
示例代码地址：[https://github.com/beego-dev/beego-example/blob/master/logger](https://github.com/beego-dev/beego-example/blob/master/logger)
