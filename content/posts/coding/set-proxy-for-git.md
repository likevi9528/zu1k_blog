---
title: 给 Git 设置代理
tags:
  - Proxy
  - GitHub
  - Git
  - Network
categories:
  - coding
date: 2020-03-27 15:45:02
---

近期，GitHub 的直连速度一直不理想，甚至被墙或者MITM，最好的解决办法就是挂代理

Git 可以使用四种不同的协议来传输资料：本地协议（Local），HTTP 协议，SSH（Secure Shell）协议及 Git 协议

而我们平时使用github一般使用两种协议，一种是http协议，另一种是ssh协议，针对这两种协议有两种不同的代理设置办法

## http协议

通常使用的链接形如：`https://github.com/git/git.git`

针对这种协议，代理设置比较简单

```bash
# http或https代理
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy
```

## ssh协议

通常使用的链接形如：`git@github.com:git/git.git` 或者 `ssh://[<username>@]<server>[:<port>]/path/repo.git`

对于使用ssh协议的库，上面的代理设置办法就失效了，需要给ssh设置socks代理

在 `~/.ssh/config` 文件中添加下面内容， 如果没有这个文件就新建

```bash
Host github.com
ProxyCommand nc -X 5 -x 127.0.0.1:1080 %h %p
```

这里使用的是nc命令，Linux需要安装netcat(一般自带了)，windows需要单独下载

windows如果安装了`mingw`也可以使用`connect`命令来替代`nc`命令

```bash
Host github.com
ProxyCommand connect -S 127.0.0.1:1080 %h %p
```

## 其他方法

### 将 ssh 协议转为 https 协议 

修改 `~/.gitconfig`，将 ssh 协议转为 https 协议:

```
[url "git@github.com:"]                                                                                                                         
        insteadOf = https://github.com/

```

### 使用 443 端口绕过防火墙

GitHub 在 ssh.github.com:443 提供了 SSH 协议，主要用于绕过企业防火墙

修改 `~/.ssh/config`

```
Host github.com
  Hostname ssh.github.com
  Port 443
  User git
```