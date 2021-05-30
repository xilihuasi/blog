---
title: hexo部署到github
date: 2016-11-25 10:56:34
tags: [hexo, github]
categories: hexo
---

之前介绍了如何搭建本地 hexo 环境，相信很多小朋友已经搭建成功了。但是写了博客只放在本地不跟大家分享肯定不是我们想要的，本文就手把手教你怎样把 hexo 部署到 github Page 上跟大家分享你的文章。<!--more-->为方便描述，文内所有的 username 表示你的 GitHub 用户名。

## 创建 GitHub Pages

GitHub Pages 的目标在于让用户可以轻易向世界分享有趣的事情，即使你不懂代码也可以轻松创建美观的页面，任何人通过浏览器都可以访问。[GitHub Pages](https://pages.github.com/)主页有详细的教程，这里就不重复造轮子了。

## 设置 SHH keys

SSH keys 是 GitHub 提供的一种不用输入密码就能识别可信的计算机的方式，通过在本地生成然后配置到你的 GitHub 账户，配置完成后便可把 GitHub 做为远程服务器进行版本控制。

1. 查看 SSH keys 是否存在
   首先打开 Git Bash（任意位置均可），输入以下命令或者直接在文件管理器 C:\Users\\[当前系统用户]\\.ssh 路径下查看本地 SSH keys。
   `ls -al ~/.ssh`
   ![](http://ofjlqteaa.bkt.clouddn.com/hexoDeploy/github1.png)
   如果有文件 id_rsa.pub 或 id_dsa.pub，则直接进入步骤 3 在 GitHub 中添加 SSH key。当然，如果你不确定这些文件何时生成或者不喜欢这些文件，直接丢进回收站然后进入步骤 2 也是完全没有任何问题的。
2. 生成新的 SSH key
   生成 public/private rsa key pair。在命令行中输入以下指令,其中email@example.com是你 GitHub 的注册邮箱。
   `ssh-keygen -t rsa -C "email@example.com"`
   ![](http://ofjlqteaa.bkt.clouddn.com/hexoDeploy/github2.png)
   设置 passphrase（可直接 enter 跳过）。设置 passphrase 后，进行版本控制时，每次与 GitHub 通信都会要求输入 passphrase，以避免误操作。

   确保 ssh-agent 已经开启:
   `eval 'ssh-agent -s'`

   将生成的 key 添加到 ssh-agent 中（如果你使用的是已经存在的 SSH key 而不是新生成的，需将*id_rsa*替换成已存在的私钥文件名），输入如下指令：
   `ssh-add ~/.ssh/id_rsa`

3. 给你的 GitHub 账号添加 SSH key
   复制 SSH key 到剪贴板上，跟踪到文件目录用编辑器打开或执行以下命令：
   `clip < ~/.ssh/id_rsa.pub`

   进入你的 GitHub 账户依次点开头像->Settings->SSH and GPG keys->New SSH key，在 Title 框中输入任何你想起的名字，Key 框中粘贴刚才复制的 SSH key，点击 Add SSH key 输入 GitHub 密码保存即可。

   输入以下命令检查 SSH 连接：
   `ssh -T git@github.com`
   ![](http://ofjlqteaa.bkt.clouddn.com/hexoDeploy/github7.png)
   你会看到一个警告信息（[for more info](https://help.github.com/articles/what-ip-addresses-does-github-use-that-i-should-whitelist/)），如果你不想详细了解输入 yes 即可。之后会看到如下信息：

   ```
   Hi username! You've successfully authenticated, but GitHub does not provide shell access.
   ```

   验证成功。

## 部署 Hexo

哈，终于到最后一步了。别急，部署之前还需要进行一项非常非常非常非常重要的配置。在 Hexo 根目录下打开 Git Bash 输入以下指令：`git remote -v`。如果返回结果为：

```
$ git remote -v
fatal: Not a git repository (or any of the parent directories): .git
```

就需要执行`git init`初始化当前目录作为 git 仓库，并且添加远程仓库。这里就不赘述了，不明白的可以转到[菜鸟网站 Git 教程](http://www.runoob.com/git/git-tutorial.html)详细了解。如果返回结果为：

```
$ git remote -v
origin  git@github.com:username/username.github.io.git (fetch)
origin  git@github.com:username/username.github.io.git (push)

```

则将地址复制，在 hexo 的配置文件\_config.yml，#Deployment 下添加如下配置：

```
deploy:
  type: git
  repo: git@github.com:username/username.github.io.git
  branch: master
```

repo 项的值就是刚才执行`git remote -v`获取到的地址。
接下来就是激动人心的 deploy 时刻啦！执行：

```
hexo generate
hexo deploy
```

执行`hexo deploy`可能会要求输入在 git 上验证的邮箱密码，输入完成后再次执行。由于网络状况不同，deploy 时间长度不等耐心等待即可。看到`INFO Deploy done:git`表明部署成功，在浏览器输入 username.github.io 就可以访问你的个人博客啦！

## 遇到的问题

1. Could not open a connection to your authenticateion agent.
   在设置 SSH keys 第二步执行`ssh-keygen -t rsa -C "email@example.com"`后，执行：
   ```
   ssh-agent -s
   ssh-add ~/.ssh/id_rsa
   ```
   遇到了如上错误。别怕此时执行：`eval 'ssh-agent -s'`（注：_使用 eval 是为了执行 ssh-agent 输出的设置环境变量的 bash 命令，以确保 ssh-add 可以通过 SSH_AUTH_SOCK 环境变量找到 ssh-agent_）再执行 ssh-add 即可，输出结果如下：
   ```
   $ eval 'ssh-agent -s'
   Agent pid 16192
   $ssh-add
   Identity added: /c/Users/Admin/.ssh/id_rsa (/c/Users/Admin/.ssh/id_rsa)
   ```
2. nothing to commit,working tree clean.Could not read Username for 'https<nolink>://github.com'：Invalid argument
   执行`hexo d`部署时，出现以上错误。原因是没有在 hexo 目录中初始化 git，需初始化并建立远程仓库。提示 Could not read Username for 'https<nolink>://github.com'是因为第三步配置 deploy:repo 写成了 https<nolink>://github.com/xilihuasi/xilihuasi.github.io.git，应参考步骤 3 配置。

## 参考文档

- [搭建 hexo 部署到 github 图文教程](http://m.paopaoche.net/new/85988)
- [Generrating an SSH key](https://help.github.com/articles/generating-an-ssh-key/)
- [Problem with deployment on GitHub](https://github.com/hexojs/hexo/issues/1495)
- [Error: Repository not found](https://help.github.com/articles/error-repository-not-found/)
