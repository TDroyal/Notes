# Rspace启动

### 一.项目需求

#### 1.前端模块：

- 用户端：

上方导航栏包括哪些：

1.首页（首页展示最新的10条博客）





2.发帖（点击进去可以，发布帖子）





3.登录/注册（未登录时显示，登录成功后显示头像）





4.头像有下拉框（下拉框里面有，我的空间，退出登录，我的收藏，）

​	4.1我的空间（有我的关注，我的粉丝，阅读量（用户点击该文章后阅读量就会+1））

​	4.2我的收藏（里面全是我收藏的帖子）

​	4.3退出登录







- 管理员端：







#### 2.后端模块：







#### 3.数据库：

##### 3.1MySQL

- user表，应该包含哪些信息：

```go
ID       
Username
Password     //存入数据库前加密
UserType     //用户类型  默认是1，默认是普通用户  0是管理员
Status       //账号状态  默认是1,激活状态
```

> GORM结构体模型

```go
type User struct {
	// gorm.Model
	ID       uint   `gorm:"column:id"`
	UserName string `gorm:"column:username;type:varchar(50);not null;unique;"`
	Password string `gorm:"column:password;type:varchar(50);not null;"` //存入数据库前加密
	UserType int    `gorm:"column:usertype;type:tinyint(10);default:1"`        // 用户类型  默认是1，默认是普通用户  0是管理员
	Status   int    `gorm:"column:status;type:tinyint(10);default:1"`          // 账号状态  默认是1,激活状态
}
```

- NormalUser表，包含如下信息：

```go
ID       //   (参考user表的ID)
Name
Gender   // 非重点
Age      // 非重点  存出生年月日
Avatar   // 头像所在的路由存储在数据库中，头像图片存储在后端的某个文件夹下
Address  // 现居地  成都
Introduction   // 个人介绍
```

> GORM结构体模型

```go
type NormalUser struct {
	ID           uint   `gorm:"column:id"`                            //默认是主键   同时也是参考User表的外键
	Name         string `gorm:"column:name;type:varchar(50);unique;"` //昵称唯一  默认是账号名username
	Gender       int    `gorm:"column:gender;type:tinyint(2);"`
	Age          int    `gorm:"column:age;type:tinyint(10);"`
	Avatar       string `gorm:"column:avatar;type:varchar(300);"` //默认是一个空头像  （可以在后端放一个空头像，大家进来默认这个url）
	Address      string `gorm:"column:address;type:varchar(100);"`
	Introduction string `gorm:"column:introduction;type:varchar(500);"`
	// User         User   `gorm:"foreignKey:ID;references:ID"` // GORM同时提供自定义外键名字的方式  参考User表的ID
}
```



##### 3.2Redis



### 二.项目编写过程记录



#### 1.启动后端：

1.创建后端文件夹：`Rspace_backend`

2.用vscode打开该文件夹，并用`terminal`进入到此文件夹，进行初始化，该命令会在项目根目录下生成`go.mod`文件（mod文件夹下存放的都是依赖文件，一些包）：

```bash
go mod init Rspace_backend
```

3.将Gin框架下载到本地（go.mod文件夹里面会多很多和Gin相关的包和依赖）

```bash
go get -u github.com/gin-gonic/gin
```

4.下载gorm以及mysql驱动

```bash
go get -u gorm.io/gorm
go get -u gorm.io/driver/mysql
```

5.接下来就可以写代码了（创建需要的文件夹，以及main.go）



#### 2.启动前端：

0.修改一下npm的镜像源为淘宝源：

```bash
npm config set registry https://registry.npmmirror.com/
```

 1.打开powershell，打开vue可视化配置界面，然后在项目根目录下创建前端即可：

```bash
vue ui
```

2.安装必要依赖，包括如下：

```bash
bootstrap
jquery
@popperjs/core
```

3.安装必要的插件，包括：

```bash
vue-router  //路由
vuex        //全局store，存储全局变量
```





#### 3.git维护

1.进到该项目的根目录，初始化git仓库

```bash
git init
```

2.将本地的更新进行提交

```bash
git add .
git commit -m "xxx"
```

3.github上点击`your repositories`，然后创建`Rspace`的远程仓库

4.然后将本地仓库和远程仓库进行关联：

```bash
git remote add origin git@github.com:TDroyal/Rspace.git
```

5.将本地的提交上传到远程仓库

```bash
git push --set-upstream origin master
```

6.后续直接`git push`进行代码维护更新即可。



### 三.技术问题总结

#### 1.跨域请求问题：

> 在前端登录界面，向后端发起请求时，报错：Access to XMLHttpRequest at 'http://127.0.0.1:9090/api/token/' from origin 'http://localhost:8080' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.

- 问题分析：

这个错误是由于浏览器的同源策略（Same-Origin Policy）导致的。同源策略要求网页只能从相同的源（协议、域名和端口）加载资源，而在你的情况下，请求的源是 `http://localhost:8080`，而目标URL是 `http://127.0.0.1:9090/api/token/`，这两个不同的源之间存在跨域请求。为了解决这个问题，你需要在服务器端进行配置，以允许来自 `http://localhost:8080` 的跨域请求。

- 解决方法：

先下载相应的包：

```bash
go get -u github.com/gin-contrib/cors
```

在下面示例中，我们使用 `gin-contrib/cors` 包来处理跨域请求。通过设置 `AllowOrigins` 字段，我们允许来自 `http://localhost:8080` 的跨域请求。你可以根据你的实际需求进行相应的配置。

```go
import (
	"github.com/gin-gonic/gin"
	"github.com/gin-contrib/cors"  //这个包需要下载
)

func main() {
	r := gin.Default()

	config := cors.DefaultConfig()
	config.AllowOrigins = []string{"http://localhost:8080"} // 允许的源地址
	config.AllowMethods = []string{"GET", "POST", "PUT", "DELETE", "OPTIONS"}
	r.Use(cors.New(config))

	// 路由和处理逻辑
	// ...

	r.Run(":9090")
}
```



#### 2.JWT身份验证（[参考文档](https://segmentfault.com/a/1190000039752568?utm_source=tag-newest)）

1.首先下载jwt包

```bash
go get -u github.com/dgrijalva/jwt-go
```

- jwt token包含的信息进行解析如下：[相关工具](https://www.lddgo.net/encrypt/jwt-decrypt)

![image-20240303202557615](./../../../images/image-20240303202557615.png)

2.更多细节欢迎查看源代码：[源代码地址](https://github.com/TDroyal/Rspace/blob/master/Rspace_backend/middleware/jwtauth_middleware.go)



#### 3.用户的密码加密（待做）





#### 4.Gin框架设置静态文件服务

- 在Gin框架中，你可以使用`gin.Static()`函数来设置静态文件服务。在该函数中，你可以指定一个URL前缀和对应的文件系统路径，用来映射到实际的文件目录。

假设你的头像文件存储在`static/avatar`文件夹下，你可以按照以下方式设置静态文件服务：在上述代码中，我们使用`router.Static("/static", "./static")`将以`/static`开头的URL映射到`./static`文件夹下的文件。这样，当你访问`http://127.0.0.1:9090/static/avatar/3905a25f4f60be208e9ed052b2b2fcb0.jpeg`时，就会返回对应的头像文件。

```go
func main() {
    router := gin.Default()

    // 设置静态文件服务
    router.Static("/static", "./static")

    router.Run(":9090")
}
```

