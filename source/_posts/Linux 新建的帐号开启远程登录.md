---
title: Linux 新建的帐号开启远程登录
date: 2024-07-03 11:57
tags: 
- Linux
- 远程登录
- 账号
- IP
categories:
- Linux
---

## 要让新创建的Linux帐号可以远程登录，您需要按照以下步骤进行设置：

1. 确保您的Linux系统上已经安装了SSH服务。如果没有安装，可以使用以下命令进行安装：
```
sudo apt install openssh-server     # 对于Debian/Ubuntu系统
sudo yum install openssh-server     # 对于CentOS/RHEL系统
```

2. 在新创建的帐号上设置密码，执行以下命令：
```
sudo passwd 新帐号的用户名
```
您将被要求输入并确认该帐号的密码。

3. 使用`sshd_config`文件来配置允许新帐号通过SSH远程登录。编辑`/etc/ssh/sshd_config`文件，找到以下行：
```
AllowUsers 用户名
```
如果没有 `AllowUsers` 行，请添加以下行：
```
AllowUsers 新帐号的用户名
```
保存并关闭文件。

4. 重启SSH服务，使修改生效：
```
sudo systemctl restart sshd
```

现在，您的新创建的Linux帐号就可以通过SSH远程登录到系统了。您可以使用SSH客户端连接到系统，并使用新帐号用户名和密码进行身份验证。

## 怎么查看系统里面已创建的账号

要查看Linux系统中已创建的帐号，您可以使用以下命令来列出系统中所有的帐号：

```
cat /etc/passwd | cut -d: -f1
```

该命令会读取`/etc/passwd`文件并列出其中的所有帐号。每个帐号的信息会以冒号分隔的字段形式给出，其中第一个字段是帐号的用户名。

另外，您也可以使用以下命令查看已激活的用户帐号：
```
cut -d: -f1 /etc/passwd
```

如果您希望进一步了解某个特定的用户帐号的详细信息，可以使用`id`命令来查看：
```
id 用户名
```

这些命令可以帮助您查看系统中已创建的用户帐号信息。

## 怎么查看本机IP
要查看本机的IP地址，您可以使用以下命令：

1. 使用 `ifconfig` 命令（在新版本的 Linux 发行版中可能已经被弃用，可以使用 `ip addr` 命令代替）:
```
ifconfig
```
或者
```
ip addr
```
在输出中，找到和您连接互联网的网络接口，通常是 `eth0` 或 `wlan0`，并查看接口的 `inet` 地址，即为本机的IP地址。

2. 使用 `hostname` 命令来查看本机的主机名及相关信息，包括IP地址：
```
hostname -I
```
这将显示本机的IP地址。

3. 可以使用 `ipconfig` 命令来查看本机的网络配置信息：
```
ipconfig
```
或者
```
ip addr show
```
这些命令将显示本机的IP地址。

以上是在Linux系统中查看本机IP地址的几种常用方法。