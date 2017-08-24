---
layout: post
title: golang Nginx+fcgi构建WEB
tags: go
comments: true
---

### 一、安装Nginx

注意关联包问题

### 二、修改nginx.conf 

vi /etc/nginx/nginx.conf

```
server {
   listen 80;
   server_name go.dev;
   root /usr/local/go/src/godev;
   index index.html;
   #gzip off;
   #proxy_buffering off;
   location / {
            try_files $uri $uri/;
   }
   location ~ /app.* {
           include         /etc/nginx/fcgi.conf;     //fastcgi配置
           fastcgi_pass    127.0.0.1:9001;
   }
   try_files $uri $uri.html =404;
}
```

### 三、在nginx.conf同一目录下创建并修改fcgi.conf配置文件

nginx配置Fastcgi解析时会调用fastcgi_params配置文件来传递服务器变量，这样CGI中可以获取到这些变量的值。默认传递以下变量： 

vi /etc/nginx/fcgi.conf

```
fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
fastcgi_param  SERVER_SOFTWARE    nginx;
fastcgi_param  QUERY_STRING       $query_string;
fastcgi_param  REQUEST_METHOD     $request_method;
fastcgi_param  CONTENT_TYPE       $content_type;
fastcgi_param  CONTENT_LENGTH     $content_length;
fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;
fastcgi_param  REQUEST_URI        $request_uri;
fastcgi_param  DOCUMENT_URI       $document_uri;
fastcgi_param  DOCUMENT_ROOT      $document_root;
fastcgi_param  SERVER_PROTOCOL    $server_protocol;
fastcgi_param  REMOTE_ADDR        $remote_addr;
fastcgi_param  REMOTE_PORT        $remote_port;
fastcgi_param  SERVER_ADDR        $server_addr;
fastcgi_param  SERVER_PORT        $server_port;
fastcgi_param  SERVER_NAME        $server_name;
# PHP only, required if PHP was built with --enable-force-cgi-redirect
fastcgi_param  REDIRECT_STATUS    200;
```

注：这边其实和fastcgi_params一样

### 四、修改好配置后，重启nginx 

  *1、进入/usr/sbin
  
```
./nginx -s reload
```

  *2、如果不行
  
```
ps -ef | grep "nginx: master process" | grep -v "grep" | awk -F ' ' '{print $2}'
```

屏幕显示的即为Nginx主进程号，例如： 6302 这时，执行以下命令即可使修改过的Nginx配置文件生效：

```
kill -HUP 6302
```
或者无需这么麻烦，找到Nginx的Pid文件：

```
kill -HUP `cat /usr/local/nginx/logs/nginx.pid`
```

或直接先kill掉再启动

### 五、编写golang构建web响应端

```golang
package main
import (
    "net"
    "net/http"
    "net/http/fcgi"
    "fmt"
)
type FastCGIServer struct{}
func (s FastCGIServer) ServeHTTP(resp http.ResponseWriter, req *http.Request) {
    resp.Write([]byte("<h1>Hello, world</h1>\n<p>Behold my Go web app.</p>"))
    fmt.Println("hello")
}
func main() {
    fmt.Println("start....")
    listener, _ := net.Listen("tcp", ":9001")
    srv := new(FastCGIServer)
    fcgi.Serve(listener, srv)
}
```

### 六、查看nginx、fcgiTest状态

```
#netstat -ntlp
```

输入nginx -t 查看是否ok

```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

现在、可以使用curl去访问URL了，或者有图形界面用browser打开

```
#curl http://go.dev/app(.*)
<h1>Hello, world</h1>
```
