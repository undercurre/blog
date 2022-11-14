---
title: goFrame初体验
date: 2022-11-13 00:10:06
tags:
---

# goFrame

## 程序启动
所有的程序入口都是由main.go进入，该文件主要是调用internal/cmd包的对应命令引导程序启动。

```
// main.go
func main() {
	cmd.Main.Run(gctx.New())
}
```

```
// cmd.go  在这里挂载路由并启动服务
var (
	Main = gcmd.Command{
		Name:  "main",
		Usage: "main",
		Brief: "start http server",
		Func: func(ctx context.Context, parser *gcmd.Parser) (err error) {
			s := g.Server()
			// 注册路由
			router.SetupRouter(ctx, s)
			s.Run()
			return nil
		},
	}
)
```

## 怎么写一个接口

### 接口定义(新手要关注go的struct和这个校验插件v)
接口注册文件位于api目录
这里将定义api的Request和Response结构体，特别是他们的校验规则
```
type UserProfileReq struct {
	g.Meta `path:"/user/profile" method:"get" tags:"UserService" summary:"Get the profile of current user"`
}
type UserProfileRes struct {
	*entity.User
}
```

### 路由注册
你要分流功能服务了嘛，所以要造路由咯！

```
func SetupRouter(ctx context.Context, s *ghttp.Server) {
	/* 用户管理 */
	s.Group("/", func(group *ghttp.RouterGroup) {
		group.Middleware(middleware.CORS, middleware.HandleResponse)
		group.Bind(controller.SysUserController)
	})
}
```

### 常量管理（譬如一些密钥啊！口令什么的！）
项目的所有常量位于internal/consts包下，供项目所有的包全局访问，常量通过不同的文件及名称命名前缀来区分不同的业务模块。
```
package consts

const (
	UserSessionKey = "123456"
)
```

### 日常MVC
路由=> 控制器=> 服务=> 数据库
```
// 譬如一个登录接口
// 控制器（负责API接口数据结构的输入与输出，调用一个或多个service实现具体的业务逻辑）

var User = cUser{}

type cUser struct{}

func (c *cUser) SignIn(ctx context.Context, req *v1.UserSignInReq) (res *v1.UserSignInRes, err error) {
	err = service.User().SignIn(ctx, model.UserSignInInput{
		Passport: req.Passport,
		Password: req.Password,
	})
	return
}

// 服务

业务模型（譬如UserSignInInput）是要自己写的不同于后面的数据模型，一般抛在model内（既非do又非entity）
type UserSignInInput struct {
	Passport string
	Password string
}

service中的代码提供注册本地服务对象的方法
使用gf gen service自动生成

然后在internal/logic包手动维护具体业务
例如判断是否登入
func (s *sUser) IsSignedIn(ctx context.Context) bool {
	if v := service.BizCtx().Get(ctx); v != nil && v.User != nil {
		return true
	}
	return false
}

业务实现的依赖注入使用的是匿名包初始化方法init，在匿名包初始化方法内部使用service.RegisterXxx的方式注入具体的接口实现对象。
func init() {
	service.RegisterUser(New())
}



最后在main.go中注册业务
_ "github.com/gogf/gf-demo-user/v2/internal/logic"

// 连接数据库

在main.go中引入以下驱动文件(mysql的)、

_ "github.com/gogf/gf/contrib/drivers/mysql/v2"


mainifest/config/config.yaml中配置数据库的信息

# Database.
database:
  logger:
    level:   "all"
    stdout:  true

  default:
    link:   "mysql:root:12345678@tcp(127.0.0.1:3306)/test"
    debug:  true

调用make dao命令后自动生成dao、do、entity代码,也就是自动实现了数据模型
```