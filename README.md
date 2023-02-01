# typecho docker 镜像

# 安装环境
```bash
typecho 1.2
docker(mysql5.7,php7.4,nginx1.3)
```

# 使用指南
```bash
1. 在服务器自定义目录创建文件夹: `mkdir website`
2. `cd website`
3. `git clone https://github.com/fireinrain/typecho-docker`
4. 修改mysql.env 中的值为自定义值(密码最好复杂一点), 修改docker-compose.yml nginx的ssl证书映射路径
5. 启动容器`docker-compose -f docker-compose.yml up -d`
6. 访问地址 http://服务器ip:18080，进入到初始化页面
7. 按照初始化的指引，完成步骤，最后将生成的代码拷贝到指定的文件夹,然后完成安装

```
# HTTPS设置
```bash
最好是在主机上安装nginx，然后申请`泛域名证书`，保存到服务器,
在DNS解析中，添加我们域名到ip的解析记录，然后再nginx的配置文件中

添加按照域名分流设置。

```

# 主机nginx配置修改
```bash
1. 所有的80 请求都转发到443端口
2. 在443端口的http中配置ssl证书
3. 然后配置多个server,不同的server 设置不同的servername

举个例子
```
```nginx.conf
server {
            listen       443 ssl;
            listen       [::]:443 ssl;
            server_name  blog.abc.xyz;
            root         /usr/share/nginx/html;


            location ^~ /abc/ {
                proxy_redirect off;
                proxy_pass http://127.0.0.1:25500/;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header REMOTE-HOST $remote_addr;
            }
            # 静态资源
            location ~ .*.(js|css|png|img|gif|jpg|woff2)$ {
            proxy_pass http://127.0.0.1:25500;
            }

            error_page 404 /404.html;
                location = /40x.html {
            }

            error_page 500 502 503 504 /50x.html;
                location = /50x.html {
            }
    }
```
### 首页js cs 无法请求问题

如果首页请求出现`This request has been blocked; the content must be served over HTTPS.`
说明页面请求了http的元素 但是主页面是https访问的.
解决方法如下：
1. 在html元素 头部插入 `<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">`
2. 修改请求api为https
```js
fetch('http://URL', {
    // ...
    referrerPolicy: "unsafe_url" 
});
```
但是以上方法对typecho都不太友好.
解决方法:
在容器的nginx中同样设置和外层nginx同样的ssl证书,把所有的请求都交给https，这样就不会有问题了。
```bash
需要修改docker-compose.yml中nginx下得ssl证书映射路径为
自己本机的ssl证书
```

如果本项目对你有帮助，star是对我最大的支持, 万分感谢！

