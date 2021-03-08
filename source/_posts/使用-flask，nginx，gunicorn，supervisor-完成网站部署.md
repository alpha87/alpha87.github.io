---
title: 使用 Flask，Nginx，Gunicorn，Supervisor 完成网站部署
tags:
  - Flask
  - Gunicorn
  - Nginx
  - Supervisor
id: '52'
categories:
  - - 编程学习
date: 2019-06-14 10:40:15
---

![](https://cdn.pixabay.com/photo/2018/05/18/15/30/webdesign-3411373__480.jpg) **宅宅生活收藏夹**小程序的 web 后端是 flask，因为只涉及到几个接口，flask 简单快速。 这篇文章记录一下整个部署的过程。
<!-- more -->
当我们使用 flask 开发完成 web 应用后，关键是部署到云服务器上，这样才能真正在网络上让用户读者访问。 部署过程看似复杂，但只要安装好这几个工具，一步一步来就没有问题。 服务器使用的版本是 Ubuntu 16.04.6 LTS。

# 安装 Flask, Gunicorn, Supervisor

因为使用的是 python3，避免 pip 安装错位置，终端输入命令： `python3 -m pip install flask gunicorn supervisor` 等待安装完成。

# Flask

写一个简单的 flask demo。

```python
# app.py

from flask import Flask
app = Flask(__name__)


@app.route('/')
def hello_world():
    return 'Hello, World!'

```

终端输入： `flask run -h 0.0.0.0 -p 8000` 如果你的云服务器安全组规则开启了 `8000` 端口，那么你在浏览器访问服务器的公网 IP，8000 端口下会显示 `Hello, World!`。 怎么配置安全组规则还请查看各自服务商的文档。 阿里云如下配置： ![配置安全组规则](https://user-images.githubusercontent.com/25655581/60378263-760bee80-9a52-11e9-9c5f-353ab3901908.png)

# 使用 gunicorn 启动程序

当我们部署到服务器上时，需要一个性能更优的`WSGI`服务器。 终端输入： `gunicorn --workers=4 --bind=0.0.0.0:8000 app:app` 如果正常启动，没有报错，在浏览器应该会看到 flask 返回的 `Hello, World!`。 `workers` 用来定义工作线程的数量，一般 `worker` 的数量为 `(2 × $num_cores) + 1`。官方文档中介绍到虽然这个公式并不十分科学，但它基于这样一个假设: 对于给定的核心数，一个工作线程将从套接字读取或写入数据，而另一个工作线程处理请求。 `bind` 用来绑定程序运行的主机地址和端口。 如果设置了SSL证书，命令为: `gunicorn --keyfile=<私钥文件> --certfile=<SSL 证书文件> --ca-certs=<CA 证书文件> --bind=0.0.0.0:443 app:app` 更多参数还请自行查看 [gunicorn 文档](https://docs.gunicorn.org/en/stable/)。

# 安装并配置 Nginx

我们需要 Nginx 作为反向代理使用。 输入以下命令安装 nginx： `apt-get install nginx -y` 安装完成后进入： `cd /etc/nginx/sites-enabled` 删除 default 文件： `rm default` 编辑新的配置文件，命名为 `app`：

```bash
server {
    listen 80;

    server_name _; # 如果有域名可以写到这里

    access_log  /var/log/nginx/access.log;
    error_log  /var/log/nginx/error.log;

    location / {
        proxy_pass         http://127.0.0.1:8000/;
        proxy_redirect     off;

        proxy_set_header   Host                 $host;
        proxy_set_header   X-Real-IP            $remote_addr;
        proxy_set_header   X-Forwarded-For      $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto    $scheme;
    }
}
```

配置文件来自 [flask 文档](http://flask.pocoo.org/docs/1.0/deploying/wsgi-standalone/#proxy-setups)。 为了避免奇怪的 bug 发生，建议把 `/etc/nginx/nginx.conf` 第一行改为 `user root;`，使用 root 用户。 输入： `nginx -t` 测试一下 nginx 配置有无问题。 没有问题的话重启服务： `service nginx restart` 有关 nginx 的配置完成。

# 使用 supervisor 管理进程

Supervisor 是一个`客户端/服务器`系统，允许其用户在类`UNIX`操作系统上控制进程。当进程被意外杀死，Supervisor 可以主动将其拉起。 使用如下命令构建配置文件： `echo_supervisord_conf > /etc/supervisord.conf` 编辑配置文件，加入以下内容：

```bash
[program:gunicorn]
user = root             ; root 用户启动
directory = /root       ; flask 文件所在目录
command = /usr/local/bin/gunicorn --workers=4 --bind=0.0.0.0:8000 app:app ; 程序启动命令（第一个 app 是 flask 的文件名，第二个是 application 的缩写）
startsecs = 5           ; 启动 5 秒后没有异常退出，视作正常启动
autostart = true        ; 在 supervisord 启动时自动启动
autorestart = true      ; 程序异常退出后重启
redirect_stderr = true  ; stderr 也重定向至 stdout
stdout_logfile = /root/logs/gunicorn.log ; stdout 日志文件，需要手动创建日志存放目录
```

`gunicorn` 路径可以这样找到：

```bash
➜  ~ which gunicorn
/usr/local/bin/gunicorn
```

输入： `supervisord -c /etc/supervisord.conf` 启动服务。 常用命令如下：（具体可查看[官方文档](http://supervisord.org/running.html#supervisorctl-actions)）

```bash
supervisorctl status                # Get all process status info.
supervisorctl stop gunicorn         # Stop a process.
supervisorctl start gunicorn        # Start a process.
supervisorctl restart gunicorn      # Restart a process Note: restart does not reread config files.
supervisorctl reread                # Reload the daemon’s configuration files, without add/remove (no restarts).
supervisorctl update                # Reload config and add/remove as necessary, and will restart affected programs.
```

全部配置完成就实现了 flask 的部署。