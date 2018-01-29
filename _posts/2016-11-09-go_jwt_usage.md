---
layout: post
title: golang jwt token存cookie遇到的坑
tags: go
comments: true
---

### 首先，关于session与jwt
<http://blog.csdn.net/pkueecser/article/details/50267125>


### 遇到的问题记录

#### 1、首先存入cookie的value值得注意是否有分号，等于号之类的特殊符号

#### 2、token值是可以直接存入的

#### 3、设置cookie

```go
cookie2:=&http.Cookie{
            Name:"adminname",
            Value:tokenString,
            Path:"/",
            MaxAge：600   //设置未来的存活时间
            //Expires: time.Unix(time.Now().Unix()+600,
        }
http.SetCookie(w,cookie2)
```

#### 4、获取cookie

```go
c1, err := r.Cookie("adminname")
if err != nil {
	log.Printf(err.Error())
	fmt.Println("Invaild token", err)
	//w.WriteHeader(http.StatusOK)
	msg := make(map[string]interface{})
	msg["msg"] = "Invaild token"
	io.WriteString(w, "Invaild token")
	//http.Redirect(w, r, "/login", http.StatusFound)
return
}
tokenString := c1.Value
```

#### 5、特别注意
w.WriteHeader(http.StatusOK)
如果先返回状态status，再set cookie，可能导致cookie无法被正常解析获取
