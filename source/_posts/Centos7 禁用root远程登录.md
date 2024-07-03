---
title: Centos7 禁用root远程登录
date: 2024-04-21 10:23
tags: 
- Centos 7
- Nginx
categories:
- Centos 7
- Nginx
---


要在CentOS 7上禁用root用户的远程登录，您可以按照以下步骤操作：

1. 以已有具有sudo权限的用户登录到CentOS 7服务器。

2. 编辑SSH配置文件`/etc/ssh/sshd_config`。您可以使用文本编辑器（如`vi`或`nano`）打开文件进行编辑。

3. 寻找以下行：

```
#PermitRootLogin yes
```

4. 将该行修改为：

```
PermitRootLogin no
```

5. 保存修改并关闭文件。

6. 重新加载SSH服务以使更改生效。您可以使用以下命令重新启动SSH服务：

```bash
sudo systemctl restart sshd
```

现在，root用户的远程登录已被禁用。如果您需要通过SSH访问服务器，请使用其他有sudo权限的用户登录，然后可以使用`su - root`切换到root用户执行需要特权的操作。 

请牢记，禁用root远程登录有助于增加服务器的安全性，因为攻击者必须先破解普通用户的密码才能尝试登录为root。如果您有任何疑问或需要进一步协助，请告诉我。