# Httplib
Httplib 主要是用来模拟客户端发送的 Http 请求。

## 基础使用
不仅支持简单的 Http 请求，还支持 Https 的请求。
```go
package main

import (
	"crypto/tls"

	"github.com/astaxie/beego/httplib"
	"github.com/astaxie/beego/logs"
)

func main() {
	// Get
	req := httplib.Get("http://beego.me/")
	str, err := req.String()
	if err != nil {
		logs.Error(err)
	}

	logs.Info(str)

	// Post
	req = httplib.Post("http://beego.me/")
	req.Param("username", "astaxie")

	// Https
	req = httplib.Get("http://beego.me/")
	req.SetTLSClientConfig(&tls.Config{InsecureSkipVerify: true}) 	// Set TLS
}
```

## Debug
可以根据返回的数据进行 Debug 调试。
```go
import (
	"github.com/astaxie/beego/httplib"
	"github.com/astaxie/beego/logs"
)

func main() {
	resp, err := httplib.Get("http://beego.me/").Debug(true).Response()
	if err != nil {
		logs.Error(err)

	}

	logs.Info(resp)
}
```

## 超时设置
```go
import (
	"time"

	"github.com/astaxie/beego/httplib"
	"github.com/astaxie/beego/logs"
)

func main() {
	// use SetTimeout(connectTimeout, readWriteTimeout)
	resp, err := httplib.Get("http://beego.me/").SetTimeout(100*time.Second, 30*time.Second).Response()
	if err != nil {
		logs.Error(err)
	}

	logs.Info(resp)
}
```

## 设置请求头部
```go
import (
	"github.com/astaxie/beego/httplib"
	"github.com/astaxie/beego/logs"
)

func main() {
	req := httplib.Post("http://beego.me/")
	req.Header("Accept-Encoding", "gzip,deflate,sdch")
	req.Header("Host", "beego.me")

	header := req.GetRequest().Header
	logs.Info(header)
}
```

## 模拟文件请求
```go
import (
	"io/ioutil"

	"github.com/astaxie/beego/httplib"
	"github.com/astaxie/beego/logs"
)

func main() {
	// Upload File
	fileReq := httplib.Post("http://beego.me/")
	fileReq.Param("username", "astaxie")
	fileReq.Param("password", "123456")
	fileReq.PostFile("uploadfile", "hello.txt")

	// Bigfile
	bigFileReq := httplib.Post("http://beego.me/")
	bt, err := ioutil.ReadFile("hello.txt")
	if err != nil {
		logs.Error(err)
	}
	bigFileReq.Body(bt)
}
```

## 引用
示例代码地址：[https://github.com/beego-dev/beego-example/tree/master/httplib](https://github.com/beego-dev/beego-example/tree/master/httplib)