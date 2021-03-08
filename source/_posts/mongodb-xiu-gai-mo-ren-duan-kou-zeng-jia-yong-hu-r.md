---
title: MongoDB 修改默认端口，增加用户认证
tags:
  - mongoDB
id: '320'
categories:
  - - 编程学习
date: 2020-03-13 15:33:50
---

为什么要修改数据库默认端口？答案无疑是为了安全。默认的端口和无密码访问，你的数据库很快就会被攻击。所以不要太天真，如果你正在使用 Mongo 数据库，不要只想着自己用着方便，小心给他人留了后门。
<!-- more -->
# 修改默认端口号

编辑 mongoDB 配置文件：

`sudo vim /etc/mongodb.conf`

修改以下两行：

```bash
bind_ip = 0.0.0.0
port = 12345
```

bind\_ip 默认是 `127.0.0.1`，只能本机访问，改为 `0.0.0.0` 就可以公网访问，port 对应端口号，可以根据自己需求修改。

**如果是云服务器记得修改安全组规则，开放对应端口才能访问！**

修改以后记得重启 mongo 服务：

`sudo /etc/init.d/mongodb restart`

重启之后，如果不输入端口号，会报错，因为默认连接 27017 端口。

![报错](https://i.loli.net/2020/03/13/BbcqvZD4fIrMP69.png)

此时再连接我们需要指定端口号：

`mongo --port 12345`

![成功](https://i.loli.net/2020/03/13/zX3VhIslfP8qK7U.png)

这样就能成功连接了。

# 增加用户认证

mongoDB 默认是无密码访问的，我们修改端口号之后仍需要增加用户认证，添加一层保障。

首先连接 mongo 数据库，使用 admin 数据库。

`use admin`

例如我现在创建一个用户名为 root，密码为 root 的用户，获取所有权限。

`db.createUser({user: 'root', pwd: 'root', roles: ['root']})`

如果注册成功，会提示：

`Successfully added user: { "user" : "root", "roles" : [ "root" ] }`

此时我们需要修改 mongo 的配置文件，找到：

```bash
# Turn on/off security.  Off is currently the default
#noauth = true
#auth = true
```

把 `auth = true` 取消注释，重启 mongo 服务。

`sudo /etc/init.d/mongodb restart`

这样我们在操作的时候必须使用密码，如果没有密码，会报权限错误。

![无权限](https://i.loli.net/2020/03/13/2Ziv3qW7kuoTgSn.png)

在登录的时候使用刚才已经注册好的用户名和密码，同时需要指定加密的数据库：

`mongo -u "root" -p "root" --authenticationDatabase "admin"`

这行命令的意思是使用用户名（`-u`）为 root，密码（`-p`）为 root，数据库（`--authenticationDatabase`）为 admin 来连接 mongo 数据库。

我现在需要给指定数据库添加用户，该怎么做呢？例如我现在有一个名为 TEST 的数据库，新增用户，赋予读写权限。

`db.createUser({user:'admin',pwd:'admin',roles:[{role:'readWrite', db:'TEST'}]})`

这样就为 TEST 数据库添加了用户。

如果需要对 TEST 数据库操作的话，在连接数据库时需要这样输入：

`mongo -u "admin" -p "admin" --authenticationDatabase "TEST"`

如果既修改了端口，也增加了用户，那么完整的连接方式是这样的：

`mongo --port 12345 -u 'admin' -p 'admin' --authenticationDatabase "TEST"`