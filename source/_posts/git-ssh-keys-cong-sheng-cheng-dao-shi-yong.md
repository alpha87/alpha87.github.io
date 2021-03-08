---
title: Git SSH keys 从生成到使用
tags:
  - git
  - github
  - SSH
  - 终端
id: '157'
categories:
  - - Linux
date: 2020-02-08 13:47:07
---

![github](https://cdn.pixabay.com/photo/2014/07/15/23/36/github-394322__480.png)

## 配置 git 用户名和邮箱

```bash
git config --global user.name "git 用户名"
git config --global user.email "git 邮箱"
```

## 生成公钥和私钥

```bash
ssh-keygen -t rsa -C "git 邮箱"
```

然后一路回车，直到生成类似的图形：

```bash
+--[ RSA 2048]----+
|                 |
|                 |
|        . E +    |
|       . o = .   |
|      . S =   o  |
|       o.O . o   |
|       o .+ .    |
|      . o+..     |
|       .+=o      |
+-----------------+
```

## 获取 id\_rsa.pub

找到 `.ssh` 目录，查看 id\_rsa.pub 文件，全部复制，接下来去 github 里配置。

以上步骤可以参考此图：

![ssh-key](https://i.loli.net/2020/02/08/RMr29GUNbasnlzH.png)

## 配置 github

![save_sshkey](https://i.loli.net/2020/02/08/SDz3yWk46fwPCvY.png)

在 github 的设置里找到 SSH and GPG key，填入名称和刚复制的内容，保存即可。

这样配置以后就可以直接使用 [git@github.com](mailto:git@github.com) 开头的仓库链接，不再输入用户名和密码。