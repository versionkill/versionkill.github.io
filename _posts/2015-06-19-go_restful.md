---
layout: post
title: golang restful
tags: go
comments: true
---

### Go Restful


### 一、prodece，consumes，xxxxparam：

        @Produces 标注返回的MIME媒体信息：

            produces(xml,json)  
	    
	    CMD => 谁在前就选谁
	    
	    Poster =〉 只要有xml就xml   （为什么？？？）
	    
        @Consumes 标注可接受的MIME媒体类型

        @PathParam、@QueryParam、@HeaderParam @CookieParam 分别标注方法的参数来自HTTP请求的不同位置。

        @PathParam 来自URL的路径

        @QueryParam 来自URL的查询参数

        @HeaderParam 来自请求头信息

        @CookieParam 来自HTTP请求的cookie	

### 二、CURD：GET、POST、PUT、DELETE、HEAD、OPTIONS：

        HEAD：用于获取到某个资源的元数据，例如，该资源是否存在，该资源的内容长度是多少等等。

        OPTIONS：请求用于获取某个资源所支持的request类型，在OPTIONS请求的Response中会包含Allow头信息，比如：allow：GET HEAD表明支持GET、HEAD

        wsContainer.Filter(wsContainer.OPTIONSFilter)

        #curl -v -X OPTIONS on http://localhost:8086/users/12

### 三、CURL或者firefox poster:

        #curl -v -H "Content-Type:application/json" -X PUT -d "{\"Id\":\"12\",\"Name\":\"Sean Plott\"}" http://127.0.0.1:8086/users/

        #curl -v -H "Content-Type:application/xml" -X PUT -d "<User><Id>2</Id><Name>john</Name></User>" http://127.0.0.1:8086/users/

### 四、数据的传递是以映射方式实现的，将json数据映射到对象中：

        usr := new(User) 

        err := request.ReadEntity(&usr)

### 五、错误机制：

        200 ok  - 成功返回状态，对应，GET,PUT,PATCH,DELETE.

        201 created  - 成功创建。

        304 not modified   - HTTP缓存有效。

        400 bad request   - 请求格式错误。

        401 unauthorized   - 未授权。

        403 forbidden   - 鉴权成功，但是该用户没有权限。

        404 not found - 请求的资源不存在

        405 method not allowed - 该http方法不被允许。

        406 Not Acceptable - 用户请求的格式不可得   （比如用户请求JSON格式，但是只有                  XML格式，需要修改consumes）

        410 gone - 这个url对应的资源现在不可用。

        415 unsupported media type - 请求类型错误。 （需要添加header的Content-Type或者修改好正确的格式）

        422 unprocessable entity - 校验错误时用。

        429 too many request - 请求过多。

        500 服务端错误    （检查命令是否存在错误，如需转义之类的）

### 六、CORS实现原理：

        CORS的本质让服务器通过新增响应头Access-Control-Allow-Origin,通过HTTP方式来实现资源共享,让每个请求的服务直接返回资源.它使用了HTTP交互

        方式来确定请求源是否有资格请求该资源,并且通过设置HTTP Header来控制访问资源的权限.

### 七、实例

```go

package main

import (
	"io"
	"log"
	"net/http"

	"github.com/emicklei/go-restful"
)

type UserResource struct{}

func (u UserResource) RegisterTo(container *restful.Container) {
	ws := new(restful.WebService)
	ws.
		Path("/api").
		Consumes("*/*").
		Produces("*/*")

	ws.Route(ws.GET("/user/{user-id}").To(u.nop))
	ws.Route(ws.GET("/produce/{user-id}").To(u.nop2))
	ws.Route(ws.POST("").To(u.nop))
	ws.Route(ws.PUT("/{user-id}").To(u.nop))
	ws.Route(ws.DELETE("/{user-id}").To(u.nop))
	
	container.Add(ws)
}

func (u UserResource) nop(request *restful.Request, response *restful.Response) {
	io.WriteString(response.ResponseWriter, "this would be a normal response")
}

func (u UserResource) nop2(request *restful.Request, response *restful.Response) {
	usrid := request.PathParameter("user-id")
	io.WriteString(response.ResponseWriter, "this would be a normal response2,"+usrid)
}

func main() {
	wsContainer := restful.NewContainer()
	u := UserResource{}
	u.RegisterTo(wsContainer)

	// Add container filter to enable CORS
	cors := restful.CrossOriginResourceSharing{
		ExposeHeaders:  []string{"X-My-Header"},
		AllowedHeaders: []string{"Content-Type"},
		CookiesAllowed: false,
		Container:      wsContainer}
	wsContainer.Filter(cors.Filter)

	// Add container filter to respond to OPTIONS
	wsContainer.Filter(wsContainer.OPTIONSFilter)

	log.Printf("start listening on localhost:8086")
	server := &http.Server{Addr: ":8086", Handler: wsContainer}
	log.Fatal(server.ListenAndServe())
}

 
```

Last but no least，github.com/emicklei/go-restful/examples 有较不错的例子，可以阅读借鉴下。
