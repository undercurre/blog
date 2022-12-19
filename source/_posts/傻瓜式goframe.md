---
title: 傻瓜式goframe
date: 2022-12-18 01:36:11
tags:
---

# 傻瓜式使用goframe

之前的是根据官方文档写出来的，是给有一定基础的人看的。现在的这段是傻瓜式小白的我的心路历程。

1. gf初始化
```
gf init my-project -u
```
2. 设计表结构，建造数据库，配置进项目
```
mainifest/config/config.yaml中配置数据库的信息

# Database.
database:
  logger:
    level:   "all"
    stdout:  true

  default:
    link:   "mysql:root:12345678@tcp(127.0.0.1:3306)/test"
    debug:  true

```
10. 引入数据库驱动在main.go中加入一行 _ "github.com/gogf/gf/contrib/drivers/mysql/v2" （如果你使用的是mysql；只需写一次）
3. 使用gf gen dao 生成对应的 dao/do/model
3. 编写api层：定义业务侧数据结构，提供对外接口的输入/输出数据结构
```go
// 示例
type UserSignInReq struct {
	g.Meta   `path:"/user/sign-in" method:"post" tags:"UserService" summary:"Sign in with exist account"`
	Passport string `v:"required"`
	Password string `v:"required"`
}
type UserSignInRes struct{}
```
5. 编写model层：定义数据侧数据结构，提供对内的数据处理的输入/输出数据结构
5. 编写logic层，自动生成service层代码。（通过配置goland File Watcher自动生成，也可以通过gf gen service手动执行脚本生成，建议前者）
6. 在service层代码生成RegisterXX()方法后，在对应的logic模块注册服务(每个模块只需要写一次)
7. 编写controller层，接收/解析用户输入的参数，调用service层的服务。
9. 引入业务到main.go中 加入一行 _ "project-name/internal/logic" （只需写一次）

8. 注册路由
```
func SetupRouter(ctx context.Context, s *ghttp.Server) {
	/* 用户管理 */
	s.Group("/", func(group *ghttp.RouterGroup) {
		group.Middleware(middleware.CORS, middleware.HandleResponse)
		group.Bind(controller.SysUserController)
	})
}
```
9. 对外暴露接口，编写cmd.go文件。
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


**其中9、10只需要添加一次；步骤6每个模块只需要写一次**