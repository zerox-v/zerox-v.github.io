---
title: 禁用 MySQL 的 root 用户
date: 2024-01-12 10:23
tags: 
- MySql
- 禁用root用户

categories:
- MySql
---

禁用 MySQL 的 root 用户通常通过修改该用户的权限或者删除该用户来实现。以下是禁用 MySQL root 用户的一般步骤：

1. 连接到 MySQL 数据库服务器：

```
mysql -u root -p
```

2. 禁用 Root 用户的远程登录权限：
```
REVOKE ALL PRIVILEGES ON *.* FROM 'root'@'%';
```

3. 删除 Root 用户远程登录权限：
```
DELETE FROM mysql.user WHERE User = 'root' AND Host = '%';
```

4. 刷新权限以使更改生效：
```
FLUSH PRIVILEGES;
```

请注意，执行这些步骤之前，请务必创建一个具有足够权限的备份用户，在禁用 root 用户之前，确保你能够访问到数据库。

希望这些步骤能帮助到你。如果你有任何疑问或者需要进一步的帮助，请随时告诉我。