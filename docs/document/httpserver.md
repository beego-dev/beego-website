# Http server
这里对于 Http 模块的使用进行简单的介绍。

## 基础
```go
import (
	"github.com/astaxie/beego"
)

func main() {
	// now you start the beego as http server.
	// it will listen to port 8080
	beego.Run()

	// it will listen to 8080
	// beego.Run("localhost")

	// it will listen to 8089
	// beego.Run(":8089")

	// it will listen to 8089
	// beego.Run("127.0.0.1:8089")
}
```

## Route
```go
import (
	"github.com/astaxie/beego"
)

func main() {

	ctrl := &MainController{}

	// we register the path / to &MainController
	// if we don't pass methodName as third param
	// beego will use the default mappingMethods
	// GET http://localhost:8080  -> Get()
	// POST http://localhost:8080 -> Post()
	// ...
	beego.Router("/", ctrl)

	// GET http://localhost:8080/health => ctrl.Health()
	beego.Router("/health", ctrl, "get:Health")

	// POST http://localhost:8080/update => ctrl.Update()
	beego.Router("/update", ctrl, "post:Update")

	// support multiple http methods.
	// POST or GET http://localhost:8080/update => ctrl.GetOrPost()
	beego.Router("/getOrPost", ctrl, "get,post:GetOrPost")

	// support any http method
	// POST, GET, PUT, DELETE... http://localhost:8080/update => ctrl.Any()
	beego.Router("/any", ctrl, "*:Any")

	beego.Run()
}

// MainController:
// The controller must implement ControllerInterface
// Usually we extends beego.Controller
type MainController struct {
	beego.Controller
}

// address: http://localhost:8080 GET
func (ctrl *MainController) Get()  {

	// beego-example/views/hello_world.html
	ctrl.TplName = "hello_world.html"
	ctrl.Data["name"] = "Get()"

	// don't forget this
	_ = ctrl.Render()
}

// GET http://localhost:8080/health
func (ctrl *MainController) Health()  {
	// beego-example/views/hello_world.html
	ctrl.TplName = "hello_world.html"
	ctrl.Data["name"] = "Health()"

	// don't forget this
	_ = ctrl.Render()
}

// POST http://localhost:8080/update
func (ctrl *MainController) Update()  {
	// beego-example/views/hello_world.html
	ctrl.TplName = "hello_world.html"

	ctrl.Data["name"] = "Update()"

	// don't forget this
	_ = ctrl.Render()
}

// GET or POST http://localhost:8080/update
func (ctrl *MainController) GetOrPost()  {
	// beego-example/views/hello_world.html
	ctrl.TplName = "hello_world.html"

	ctrl.Data["name"] = "GetOrPost()"

	// don't forget this
	_ = ctrl.Render()
}

// any http method http://localhost:8080/any
func (ctrl *MainController) Any()  {
	// beego-example/views/hello_world.html
	ctrl.TplName = "hello_world.html"

	ctrl.Data["name"] = "Any()"

	// don't forget this
	_ = ctrl.Render()
}
```

## Cookie
```go
import (
	"github.com/astaxie/beego"
)

// to test this
// you should browse POST http://localhost:8080/session to set session firstly
func main() {

	// create contr
	ctrl := &MainController{}

	// POST http://localhost:8080/cookie => ctrl.PutCookie()
	beego.Router("/cookie", ctrl, "post:PutCookie")

	// GET http://localhost:8080/cookie => ctrl.ReadCookie()
	beego.Router("/cookie", ctrl, "get:ReadCookie")

	beego.Run()
}

type MainController struct {
	beego.Controller
}

func (ctrl *MainController) PutCookie()  {
	// put something into cookie
	ctrl.Ctx.SetCookie("name", "beego cookie")

	// beego-example/views/hello_world.html
	ctrl.TplName = "hello_world.html"
	ctrl.Data["name"] = "PutCookie"
	_ = ctrl.Render()
}

func (ctrl *MainController) ReadCookie()  {
	// beego-example/views/hello_world.html
	ctrl.TplName = "hello_world.html"
	ctrl.Data["name"] = ctrl.Ctx.GetCookie("name")
	// don't forget this
	_ = ctrl.Render()
}
```

## Session
```go
import (
	"github.com/astaxie/beego"
)

// to test this
// you should browse POST http://localhost:8080/session to set session firstly
func main() {

	// enable session
	// or you can put "session=true" into your config file
	beego.BConfig.WebConfig.Session.SessionOn = true

	// create contr
	ctrl := &MainController{}

	// POST http://localhost:8080/session => ctrl.PutSession()
	beego.Router("/session", ctrl, "post:PutSession")

	// GET http://localhost:8080/session => ctrl.ReadSession()
	beego.Router("/session", ctrl, "get:ReadSession")

	beego.Run()
}

type MainController struct {
	beego.Controller
}

func (ctrl *MainController) PutSession()  {
	// put something into session
	ctrl.SetSession("name", "beego session")

	// beego-example/views/hello_world.html
	ctrl.TplName = "hello_world.html"
	ctrl.Data["name"] = "PutSession"
	_ = ctrl.Render()
}

func (ctrl *MainController) ReadSession()  {
	// beego-example/views/hello_world.html
	ctrl.TplName = "hello_world.html"
	ctrl.Data["name"] = ctrl.GetSession("name")
	// don't forget this
	_ = ctrl.Render()
}
```

## Middleware
### 自定义 Middleware
```go
import (
	"fmt"
	"net/http"

	"github.com/astaxie/beego"
	"github.com/astaxie/beego/logs"
)

func main() {

	beego.Router("/", &MainController{})
	// The middleware definition is func(http.Handler) http.Handler
	beego.RunWithMiddleWares(":8080", customMiddleware1, func(next http.Handler) http.Handler {
		return &customMiddleware2{
			next: next,
		}
	})

	// start the server and then request GET http://localhost:8080/
	// and then you will see:
	// log: in the custom middleware 1 from: /
	// log: in the custom middleware 2 from: /
}

// customMiddleware1 is a simple way to custom your middleware
// don't forget invoke next handler
func customMiddleware1(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		logs.Info("log: in the custom middleware 1 from: %s\n", r.RequestURI)
		next.ServeHTTP(w, r)
	})
}

// customMiddleware2: usually you should think about using singleton design pattern
// and don't forget invoke next handler
type customMiddleware2 struct {
	next http.Handler
}

func (c *customMiddleware2) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	logs.Info("log: in the custom middleware 2 from: %s\n", r.RequestURI)
	c.next.ServeHTTP(w, r)
}

type MainController struct {
	beego.Controller
}

func (m *MainController) Get() {
	m.Ctx.WriteString(fmt.Sprintf("hello world"))
}
```

### 第三方 Middleware
```go
import (
	"fmt"

	"github.com/astaxie/beego"
	apmbeego "github.com/opentracing-contrib/beego"
)

func main() {
	beego.Router("/", &MainController{})
	// Actually, I just pick the opentracing-contrib/beego as example but I do not check
	// whether it is a good middleware
	beego.RunWithMiddleWares("localhost:8080", apmbeego.Middleware("bee-go-demo"))

	// start the server and then request GET http://localhost:8080/
}

type MainController struct {
	beego.Controller
}

func (m *MainController) Get() {
	m.Ctx.WriteString(fmt.Sprintf("hello world"))
}
```

## 引用
示例代码地址：[https://github.com/beego-dev/beego-example/blob/master/httpserver](https://github.com/beego-dev/beego-example/blob/master/httpserver)