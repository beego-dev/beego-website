# Utils

## 验证码
beego 中可以直接使用封装好了的验证码的功能，使用的时候先实例化 captcha 对象，并且在 init 时可以把设置验证码相关的配置。

### 初始化
```go
// init captcha
var cpt *captcha.Captcha

func init() {
	store := cache.NewMemoryCache()
	cpt = captcha.NewWithFilter("/captcha/", store)
	cpt.ChallengeNums = 4
	cpt.StdWidth = 100
	cpt.StdHeight = 40
}
```

### 具体使用
在这之后，就可以使用我们的 captcha 功能了（这里使用页面进行展示，可以根据源代码进行快速体验）：
```go
// Controller -
type Controller struct {
	beego.Controller
}

func main() {
	ctrl := &Controller{}

	beego.Router("/", ctrl)
	beego.Router("/sendCaptcha", ctrl, "post:Captcha")

	beego.Run()
}

// Get - address: http://127.0.0.1:8080/
func (ctrl *Controller) Get() {
	ctrl.TplName = "captcha.html"
	ctrl.Data["name"] = "Home"

	// don't forget this
	_ = ctrl.Render()
}

// Captcha - address: http://127.0.0.1:8080/sendCaptcha
func (ctrl *Controller) Captcha() {
	ctrl.TplName = "captcha.html"

	// verify code
	if !cpt.VerifyReq(ctrl.Ctx.Request) {
		logs.Error("Captcha does not match")
		_ = ctrl.Render()
		return
	}

	logs.Info("matched")
	_ = ctrl.Render()
}
```

### 引用
示例代码地址：[https://github.com/beego-dev/beego-example/tree/master/captcha](https://github.com/beego-dev/beego-example/tree/master/captcha)