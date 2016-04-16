## 部署nodejs应用

### 基础

- git clone
- npm i
- pm2 start


### 修改nginx

```
cat /etc/nginx/sites-enabled/default


upstream backend_nodejs {
    server 127.0.0.1:3019 max_fails=0 fail_timeout=10s;
    #server 127.0.0.1:3001;
    keepalive 512;
}


server {
	listen 80 default_server;
	listen [::]:80 default_server ipv6only=on;

	#root /usr/share/nginx/html;
	root /home/sang/workspace/oschina/base2-wechat-jssdk/public;
	index index.html index.htm;

	# Make site accessible from http://localhost/
        server_name nodeonly.mengxiaoban.cn at35.com;
	client_max_body_size 16M;
  	keepalive_timeout 10;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		#try_files $uri $uri/ =404;
		# Uncomment to enable naxsi on this location
		# include /etc/nginx/naxsi.rules

    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    proxy_redirect off;
		proxy_next_upstream error timeout http_500 http_502 http_503 http_504;
    proxy_set_header   Connection "";
    proxy_http_version 1.1;
    proxy_pass http://backend_nodejs;
	}
}
```

注意

- upstream backend_nodejs定义的代理转发的api地址
- location /下面的proxy_pass，从upstream里取
- root下面放的是静态资源，比如express下的public目录


然后重启nginx即可

```
sudo nginx -s reload
```

