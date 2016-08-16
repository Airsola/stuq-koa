# cors跨域问题

## cors套件

- https://github.com/evert0n/koa-cors
- https://github.com/expressjs/cors

## 使用nginx

- nginx
  - 前端：moa-frontend
    - public下面的采用nginx做反向代理
    - 其他的采用express+jade精简代码（ajax与后端交互）
  - 后端

```
    server {
        listen       8000;
        server_name  localhost;
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        location / {
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
      	    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
      	    proxy_set_header X-NginX-Proxy true;
      	    proxy_pass http://127.0.0.1:3010;
            proxy_redirect off;
        } 
        
        location /api {
            proxy_set_header X-Real-IP $remote_addr;
      	    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
      	    proxy_set_header X-NginX-Proxy true;
      	    proxy_pass http://127.0.0.1:3005;
            proxy_redirect off;
        }
    }
```

nginx默认是80端口，所以`/` 和 `/api` 是同源的.

这其实也是前后端分离比较好的实践。

## 浏览器上

For OSX,终端里

```
$ open -a Google\ Chrome --args --disable-web-security
```

For Linux :

```
$ google-chrome --disable-web-security
```

如果你也想尝试去访问本地文件，用于开发，比如ajax或json，你也可以用下面这个flag

```
-–allow-file-access-from-files
```

For Windows
```
chrome.exe --disable-web-security
```
