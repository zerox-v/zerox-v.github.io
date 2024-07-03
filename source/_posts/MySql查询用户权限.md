---
title: MySql查询用户权限
date: 2024-05-12 10:23
tags: 
- MySql
- 账户权限

categories:
- MySql
---

要查看 MySQL 中各个账户的权限，可以执行以下步骤：

1. **连接到 MySQL 数据库服务器**：

   ```
   mysql -u root -p
   ```

2. **查看所有用户的权限**：

   ```
   SELECT User, Host, Select_priv, Insert_priv, Update_priv, Delete_priv, Create_priv, Drop_priv, Reload_priv, Shutdown_priv, Process_priv, File_priv, Grant_priv, References_priv, Index_priv, Alter_priv, Show_db_priv, Super_priv, Create_tmp_table_priv, Lock_tables_priv, Execute_priv, Repl_slave_priv, Repl_client_priv, Create_view_priv, Show_view_priv, Create_routine_priv, Alter_routine_priv, Create_user_priv, Event_priv, Trigger_priv, Create_tablespace_priv FROM mysql.user;
   ```

   以上查询语句将列出所有用户及其对应的权限。您可以根据实际需求选择查看的权限。

3. **查看特定用户的权限**：

   如果您只想查看特定用户的权限，可以使用类似以下查询语句：

   ```
   SELECT * FROM mysql.user WHERE User = 'username';
   ```

   请将 `'username'` 替换为您要查看的用户名。

通过以上步骤，您可以查看 MySQL 中各个账户的权限情况。如果您需要进一步帮助或有其他问题，请随时告诉我。