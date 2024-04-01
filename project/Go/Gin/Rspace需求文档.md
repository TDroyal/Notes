# Rspace启动

### 一.项目需求

#### 1.前端模块：

- 用户端：

上方导航栏包括哪些：

1.首页（首页展示最新的5条博客）

直接去post表，查询创建时间最新的5条posts，进行展示即可

```go
// 去post表按时间顺序（降序）查找前5的帖子并且连表（normaluser表）查询出用户的姓名和id，一起返回给前端
```

```go
// 最下面还有一个菜单栏，包括收藏和评论，其中评论也往下拉一个显示评论的窗口。
```



**待做：**每个帖子的右上角可以点击进入查看详情界面，详情界面的右上角有编辑和删除按钮。





2.发帖（点击进去可以，发布帖子）



每个帖子右上角有一个查看原文的按钮，可以进入查看原文的页面（如果该帖子是自己的话，那么可以编辑和删除）



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
	ID           uint    `gorm:"column:id" form:"id" json:"id" xml:"id"`                                  //默认是主键   同时也是参考User表的外键
	Name         string  `gorm:"column:name;type:varchar(50);unique;" form:"name" json:"name" xml:"name"` //昵称唯一  默认是账号名username
	Gender       int     `gorm:"column:gender;type:tinyint(2);" form:"gender" json:"gender" xml:"gender"`
	Age          *int    `gorm:"column:age;type:tinyint(10);" form:"age" json:"age" xml:"age"`              // A pointer to a string, allowing for null values
	Avatar       string  `gorm:"column:avatar;type:varchar(300);" form:"avatar" json:"avatar" xml:"avatar"` //默认是一个空头像  （可以在后端放一个空头像，大家进来默认这个url）
	Address      *string `gorm:"column:address;type:varchar(100);" form:"address" json:"address" xml:"address"`
	Introduction *string `gorm:"column:introduction;type:varchar(500);" form:"introduction" json:"introduction" xml:"introduction"`
	Posts        []Post  `gorm:"foreignKey:UserID;references:ID"` //Post表的UserID为外键，参考NormalUser表的ID
	// 一对多关联属性，表示一个用户有多个帖子
}
```

- Post表，包含如下信息：

```go
ID         //帖子的ID
CreatedAt    time.Time      // Automatically managed by GORM for creation time
UpdatedAt    time.Time      // Automatically managed by GORM for update time
DeletedAt gorm.DeletedAt `gorm:"index"`
Type       //帖子的类型
Content    //帖子内容   不能为空
Image      //帖子的图片(可能有多个图片，在数据库中存每个图片的名称，用@隔开)
UserID     //用户ID，外键参考NormalUser表的ID

```

> GORM结构体模型

```go
type Post struct {
	gorm.Model
	Content string  `gorm:"column:content;type:varchar(500);"`
	Image   *string `gorm:"column:image;type:varchar(500);"`        //图片可以为空以@#@分隔开
	Type    int     `gorm:"column:type;type:tinyint(3);default:1;"` //默认是1share
	UserID  uint    `gorm:"column:user_id;"`                        //外键名
}

//考虑加入一个Comments成员

// 后续加入一个收藏的成员，返回给前端用于判断当前用户是否收藏？？？？，后续考虑
//需要添加一个冗余信息吗，该帖子对应的评论数和收藏数量？？？， 后续考虑
```

- Comment表，包含如下信息：(一个帖子包含多个评论，一个评论属于一个帖子，一个评论对应一个用户)

```go
ID   //评论id
CreatedAt    time.Time      // Automatically managed by GORM for creation time
UpdatedAt    time.Time      // Automatically managed by GORM for update time
DeletedAt gorm.DeletedAt `gorm:"index"`
PostID     //参考Post表的帖子ID
UserID         //用户ID，外键参考NormalUser表的ID
Comment        //评论的内容
```

> GORM结构体模型

```go
type Comment struct {
	gorm.Model
	Comment string `gorm:"column:Comment;type:varchar(400);"`
	PostID  uint   `gorm:"column:post_id;"`
	UserID  uint   `gorm:"column:user_id;"`
	// 我需要返回对应的发布评论的用户id和用户姓名，用户avatar
}
```

- Collection表（收藏表），包含如下信息：（一个帖子有多个人多个收藏，一个收藏属于一个帖子，一个收藏对应一个用户，一个用户有多个收藏）

```go
ID // 收藏id
CreatedAt    time.Time      // Automatically managed by GORM for creation time
UpdatedAt    time.Time      // Automatically managed by GORM for update time     后续自己的收藏空间显示帖子按照这个时间降序排序，最新收藏时间排序
UserID        //外键参考NormalUser表的ID
PostID        //外键，参考Post表的帖子ID
Status        //状态是否收藏，默认为1：是收藏了   0为未收藏
// （用户点击收藏时需要先判断之前是否收藏过，根据PostID和UserID去数据库找，如果存在，就改Status=1，否则插入新的数据即可）
//用户第一次点击时收藏，插入新的数据，status默认为1，则红星
//用户取消收藏时，直接修改Status为0即可。
//用户再次点击收藏时，直接修改Status为1即可。
```

> GORM结构体模型

```go
type Collection struct {
	ID        uint
	CreatedAt time.Time // Automatically managed by GORM for creation time
	UpdatedAt time.Time // Automatically managed by GORM for update time     后续自己的收藏空间显示帖子按照这个时间降序排序，最新收藏时间排序
	PostID    uint      `gorm:"column:post_id;"`
	UserID    uint      `gorm:"column:user_id;"`
	Status    *int      `gorm:"column:status; type:tinyint(3); default:1;"`    // 可以存0
}

```

- Follow表（关注表），包含如下信息：（一个用户有多个用户关注）

```go
ID   //关注id
CreatedAt    time.Time      // Automatically managed by GORM for creation time
UpdatedAt    time.Time      // Automatically managed by GORM for update time     后续查看粉丝列表或关注列表时按这个关注时间降序排序，最新的关注或者粉丝在前面
IsFollowedUserID   //被关注者的id
FollowedUserID     //对应的关注者id
Status             //默认为1：关注了， 0为取消关注。
// （用户点击关注时需要先判断之前是否关注过，根据IsFollowedUserID和FollowedUserID去数据库找，如果存在，就改Status=1，否则插入新的数据即可）
//用户第一次点击关注时，插入新的数据，status默认为1，关注成功
//用户点取消关注时，直接修改Status为0即可。
//用户再次点击关注时，直接修改Status为1即可。
```

> GIORM：在normal_user表中创建many2many，[参考官网](https://gorm.io/zh_CN/docs/many_to_many.html)，这里我不使用many2many，我自己建表







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

##### 基本流程：

​	官网地址：[官网](https://pkg.go.dev/github.com/dgrijalva/jwt-go#example-Parse--Hmac)

![1711854220812](./../../../images/1711854220812.png)

​	1.下载：

```bash
go get -u github.com/dgrijalva/jwt-go
```

​	2.基本用法：

```go
package main

import (
	"fmt"
	"time"

	"github.com/dgrijalva/jwt-go"
)

type UserClaims struct {
	ID        uint   `json:"user_id"`
	UserName  string `json:"username"`
	TokenType string `json:"token_type"` //jwt, refresh_jwt
	jwt.StandardClaims
}

// 密钥
var (
	secretKey = []byte("royal_111")
)

// 生成token
func GenerateToken() string {
	userClaim := &UserClaims{
		ID:        1,
		UserName:  "royal_111",
		TokenType: "jwt",
		StandardClaims: jwt.StandardClaims{
			NotBefore: int64(time.Now().Unix() - 1000), // 签名生效时间
			ExpiresAt: int64(time.Now().Unix() + 600),  // 签名过期时间  10分钟  60 * 10
			Issuer:    "royal_111",                     // 签名颁发者
		},
	}
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, userClaim)

	tokenStr, _ := token.SignedString(secretKey)

	fmt.Println(tokenStr) //生成的token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6InJveWFsXzExMSIsInRva2VuX3R5cGUiOiJqd3QifQ.p0hZXnnk9D9ON7xPXV_iehaLSocNEEBjEgiW-sQref8
	return tokenStr
}

// 解析token
func ParserToken(tokenStr string) (*UserClaims, error) {
	var claim UserClaims
	token, err := jwt.ParseWithClaims(tokenStr, &claim, func(t *jwt.Token) (interface{}, error) { //第一个传密钥， 第二个传err
		return secretKey, nil
	})

	if err != nil {
		return nil, err
	}

	if token.Valid {
		// fmt.Println(claim) //解析出来的结果 {1 royal_111 jwt { 1711864893  0 royal_111 1711863293 }}
		return &claim, nil
	}
	return nil, err
}

func main() {
	token := GenerateToken()
	claim, _ := ParserToken(token)
	fmt.Println(claim.ID, claim.UserName)  //claim就是解析出来的用户信息
}

/*
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6InJveWFsXzExMSIsInRva2VuX3R5cGUiOiJqd3QiLCJleHAiOjE3MTE4NjU0ODgsImlzcyI6InJveWFsXzExMSIsIm5iZiI6MTcxMTg2Mzg4OH0.YcLUMeSERd6pSl5CMDf4G2ZHkj0keyoAZ4FeQS8_mWA
1 royal_111 &{1 royal_111 jwt { 1711865518  0 royal_111 1711863918 }}
*/
```

#### 3.用户的密码加密

```go
import (
	"crypto/md5"
	"fmt"
)

// 对密码加密的中间件
func getMd5(password string) string {
	// return fmt.Sprintf((md5.Sum()))
	return fmt.Sprintf("%x", md5.Sum([]byte(password)))
}

func main() {
	password := "123456"
	fmt.Println(getMd5(password)) //e10adc3949ba59abbe56e057f20f883e  长度为32
}
```





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



#### 5.前端传过来空字符串时，后端应该怎么接受，并且正确存入数据库

- 例如前端传过来的地址和兴趣爱好都为空，即age=0，address=""，introduction=""，请问后端应该怎么成功接受，并且正确把这种空字符串存入数据库。

将允许为空的字段改为对应的指针即可：

这是获取前端数据的结构体：

```go
type UpdatedUserInfo struct {
	Name         string  `form:"name" json:"name" xml:"name"  binding:"required"` //姓名不能为空，如果是指针类型，就可以为空，不报错；否则报错Key: 'UpdatedUserInfo.Name' Error:Field validation for 'Name' failed on the 'required' tag
	Gender       int     `form:"gender" json:"gender" xml:"gender"  binding:"required"`
	Age          *int    `form:"age" json:"age" xml:"age"  binding:"required"`
	Address      *string `form:"address" json:"address" xml:"address"  binding:"required"` // 设置为指针类型，即使有binding:"required"，也可以为空
	Introduction *string `form:"introduction" json:"introduction" xml:"introduction"  binding:"required"`
}
```

这个是数据库对应的模型结构体：

```go
type NormalUser struct {
	ID           uint    `gorm:"column:id" form:"id" json:"id" xml:"id"`                                  //默认是主键   同时也是参考User表的外键
	Name         string  `gorm:"column:name;type:varchar(50);unique;" form:"name" json:"name" xml:"name"` //昵称唯一  默认是账号名username
	Gender       int     `gorm:"column:gender;type:tinyint(2);" form:"gender" json:"gender" xml:"gender"`
	Age          *int    `gorm:"column:age;type:tinyint(10);" form:"age" json:"age" xml:"age"`              // A pointer to a string, allowing for null values
	Avatar       string  `gorm:"column:avatar;type:varchar(300);" form:"avatar" json:"avatar" xml:"avatar"` //默认是一个空头像  （可以在后端放一个空头像，大家进来默认这个url）
	Address      *string `gorm:"column:address;type:varchar(100);" form:"address" json:"address" xml:"address"`
	Introduction *string `gorm:"column:introduction;type:varchar(500);" form:"introduction" json:"introduction" xml:"introduction"`
}
```

#### 6.前端发出Delete请求，后端接受不到data数据

- 前端需要把data转为json格式的数据`JSON.stringify({ comment_id: comment_id }),`，然后后端用`c.BindJSON(&id)`绑定获取前端传过来的`json`数据。

- 前端代码：

```js
const deleteAComment = (index, post_id,comment_id)=>{  //获得comment的id删
            console.log(comment_id)
            //删完后，重新查询一次评论
            $.ajax({
                url:"http://127.0.0.1:9090/homepost/deletecommentsbycommentid/",
                type:"DELETE",
                // data:{
                //     comment_id: comment_id,
                // },
                data:JSON.stringify({ comment_id: comment_id }),  //必须这样写？delete???
                // params:{
                //     comment_id:comment_id,
                // },
                headers:{
                    'Authorization': "Bearer " + store.state.user.jwt,
                },
                success(resp){
                    if(resp.status === 0) {
                        ElMessage.success("成功删除评论")
                        get_comments_by_post_id(index, post_id)
                        return 
                    }
                    ElMessage.error("删除评论失败")
                },
                error(resp) {
                    ElMessage.error("删除评论失败")
                    console.log(resp)
                }
            })
        }
```

- 后端代码

```go
type GetCommendID struct {
	CommendID uint `form:"comment_id" json:"comment_id" xml:"comment_id" binding:"required"`
}

func DeleteCommentsByCommentId(c *gin.Context) {
	var id GetCommendID
	// delete前端传json过来，我才能获取到数据
	if err := c.BindJSON(&id); err != nil {
		c.JSON(http.StatusOK, gin.H{
			"status":  -1,
			"message": "delete comment unsuccess",
			"data":    nil,
		})
		return
	}
	if err := dao.DB.Delete(&models.Comment{}, id.CommendID).Error; err != nil {
		c.JSON(http.StatusOK, gin.H{
			"status":  -1,
			"message": "delete comment unsuccess",
			"data":    nil,
		})
		return
	}
	c.JSON(http.StatusOK, gin.H{
		"status":  0,
		"message": "delete comment success",
		"data":    nil,
	})
}
```

#### 7.发送验证码，注册登录功能的实现。

​	[官网地址](https://github.com/jordan-wright/email)

​	1.下载

```bash
go get github.com/jordan-wright/email
```

​	2.使用qq邮箱开启服务：[参考方法](https://zhuanlan.zhihu.com/p/664771819)

​	3.第2步会得到一个第三方登录QQ邮箱的授权码pyzlylyfahxsdada，然后用在配置里面：

```go
import (
	"crypto/tls"
	"fmt"
	"math/rand"
	"net/smtp"
	"strconv"
	"time"

	"github.com/jordan-wright/email"
)

// 发送验证码
func SendCodeByEmail(toUserEmail, code string) error {
	e := email.NewEmail()
	e.From = "royal_111 <royal_3@qq.com>" // 发送者
	e.To = []string{toUserEmail}          //接收者
	e.Subject = "Welcome to Rspace"       //主题
	// e.Text = []byte("Text Body is, of course, supported!") //发送文本
	e.HTML = []byte("您的验证码是<b>" + code + "</b>") //发送html
	// if err := e.Send("smtp.qq.com:465", smtp.PlainAuth("", "royal_3@qq.com", "pyzlylyfahxsdada", "smtp.qq.com:465")); err != nil {
	// 	return err  //报错//EOF
	// }
	if err := e.SendWithTLS("smtp.qq.com:465", smtp.PlainAuth("", "royal_3@qq.com", "pyzlylyfahxsdada", "smtp.qq.com:465"), &tls.Config{ServerName: "smtp.qq.com:465", InsecureSkipVerify: true}); err != nil { //InsecureSkipVerify: true跳过验证
		return err
	}
	return nil
}

// helper.go // 随机生成验证码
func generateRandCode() string {
	//随机数种子设置
	source := rand.NewSource(time.Now().UnixNano())
	random := rand.New(source)
	s := ""
	for i := 0; i < 6; i++ {
		s += strconv.Itoa(random.Intn(10))
	}
	return s
}

func main() {
	// fmt.Println(generateRandCode())
	err := SendCodeByEmail("royal_111@qq.com", generateRandCode())
	fmt.Println(err) // <nil>
}
```

![image-20240331145530817](./../../../images/image-20240331145530817.png)

