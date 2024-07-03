---
title: Centos 7 yum 安装nginx
date: 2024-03-13 10:23
tags: 
- Centos 7
- Nginx
categories:
- Centos 7
- Nginx
---
要在Centos 7上使用yum安装nginx，您可以按照以下步骤操作：

1. 打开终端或SSH连接到您的Centos 7服务器。
2. 确保您具有sudo权限或root权限。
3. 运行以下命令更新yum包管理器的软件包索引：

```bash
sudo yum update
```

4. 运行以下命令安装nginx：

```bash
sudo yum install nginx
```

5. 确认安装过程中会提示您是否要继续安装nginx，输入'y'并按Enter继续安装。
6. 安装完成后，启动nginx服务并设置它在系统引导时自动启动：

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

7. 检查nginx服务是否正在运行：

```bash
sudo systemctl status nginx
```

8. 确保防火墙允许HTTP和HTTPS流量通过。如果防火墙已启用，可以运行以下命令：

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

现在，您已经成功在Centos 7上使用yum安装了nginx。您可以通过访问服务器的IP地址或域名来验证nginx是否已成功安装并正常运行。