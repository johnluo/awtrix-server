# awtrix-server
## 项目简介
**awtrix-server** 是基于原版 [AWTRIX 2.0 Server](https://blueforcer.de/awtrix-2-0/) 的 docker 镜像.由于原版项目的环境安装对于新手来说相对复杂,推荐使用docker快速部署项目,方便存储和迁移配置.
- GitHub [stilleshan/awtrix-server](https://github.com/stilleshan/awtrix-server)
- Docker [stilleshan/awtrix-server](https://hub.docker.com/r/stilleshan/awtrix-server)

### 镜像简介
- 原版项目awtrix.jar
- 容器中更改为中国时区
- 挂载目录,方便存储,迁移配置及插件等文件.

> **详情请参阅Dockerfile**

### 群晖Docker部署图文教程
- [群晖NAS高级服务 - docker 部署 AWTRIX 2.0 Server](https://www.ioiox.com/archives/76.html)


## 运行部署
### 下载配置文件
下载或者 **git clone** 本仓库,复制 **awtrix** 文件夹到你指定的目录,建议存放至 **/root** 下.
```shell
cd /root
git clone https://github.com/stilleshan/awtrix-server.git
cd awtrix-server
mv awtrix /root
```

### 启动容器
```shell
docker run -d --name=awtrix --restart=always -p 7000:7000 -p 7001:7001 -v /root/awtrix:/awtrix stilleshan/awtrix-server
```
### 访问Web
http://服务器IP:7000
> **服务器或控制台开放7000及7001端口.**

## 反向代理配置
参考以下 Nginx 示例来配置反向代理.  
更多 Nginx 安全访问及配置 WebSocket 支持来解决 Loading Applist 问题,请参考详细教程:
- [Linux 服务器 docker 部署 AWTRIX 2.0 Server](https://www.ioiox.com/archives/75.html)

### Nginx示例
```nginx
upstream awtrix { 
    server 127.0.0.1:7000;
}

server {
    listen 80;
    server_name  yourdomain.com;
    return 301 https://yourdomain.com$request_uri;
}

server {
    listen 443 ssl;
    server_name  yourdomain.com;
    gzip on;    

    auth_basic "Restricted";
    auth_basic_user_file /usr/local/nginx/conf/vhost/awtrix;

    ssl_certificate /usr/local/nginx/conf/ssl/yourdomain.crt;
    ssl_certificate_key /usr/local/nginx/conf/ssl/yourdomain.key;

    location / {
        proxy_redirect off;
        proxy_pass http://awtrix;

        proxy_set_header  Host                $http_host;
        proxy_set_header  X-Real-IP           $remote_addr;
        proxy_set_header  X-Forwarded-Ssl     on;
        proxy_set_header  X-Forwarded-For     $proxy_add_x_forwarded_for;
        proxy_set_header  X-Forwarded-Proto   $scheme;
        proxy_set_header  X-Frame-Options     SAMEORIGIN;

		proxy_http_version 1.1;
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection "upgrade";
		proxy_read_timeout 86400;

        client_max_body_size        100m;
        client_body_buffer_size     128k;

        proxy_buffer_size           4k;
        proxy_buffers               4 32k;
        proxy_busy_buffers_size     64k;
        proxy_temp_file_write_size  64k;
    }
}
```

## 相关连接
- 原版项目官网 [AWTRIX 2.0](https://blueforcer.de/awtrix-2-0/)
- GitHub [stilleshan/awtrix-server](https://github.com/stilleshan/awtrix-server)
- Docker [stilleshan/awtrix-server](https://hub.docker.com/r/stilleshan/awtrix-server)
- [Linux 服务器 docker 部署 AWTRIX 2.0 Server](https://www.ioiox.com/archives/75.html)
- [群晖NAS高级服务 - docker 部署 AWTRIX 2.0 Server](https://www.ioiox.com/archives/76.html)