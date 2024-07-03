---
title: Centos 7 运维
date: 2024-07-21 10:23
tags: 
- Centos 7
categories:
- Centos 7
---

要在CentOS 7上创建具有root权限的新用户，您可以按照以下步骤操作：

1. 以root用户身份登录到CentOS 7服务器。
2. 使用`adduser`命令创建新用户。将`<username>`替换为所需的用户名。

```bash
adduser <username>
```

3. 为新用户设置密码。

```bash
passwd <username>
```

4. 将新用户添加到`wheel`组以赋予他们sudo（root）权限。

```bash
usermod -aG wheel <username>
```

5. 验证新用户是否已添加到`wheel`组。

```bash
groups <username>
```

6. 编辑sudoers文件以允许`wheel`组的成员使用sudo执行命令。

```bash
visudo
```

通过删除以下行开始的`#`取消注释：

```bash
%wheel ALL=(ALL) ALL
```

7. 保存并退出sudoers文件。

8. 现在新用户应该在CentOS 7上具有root权限。您可以使用`su`命令切换到新用户：

```bash
su - <username>
```

请确保仔细遵循这些步骤，并将`<username>`替换为您要创建的实际用户名。如果需要进一步帮助，请告诉我。

