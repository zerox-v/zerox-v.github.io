---
title: Centos 7通过certbot 自动给nginx的ssl证书续期
date: 2025-02-21 00:02
tags: 
- Centos 7
- certbot
- nginx
- SSL

categories:
- Centos 7
---

在 CentOS 7 上使用 `certbot` 自动为 Nginx 续期 SSL 证书的步骤如下：

---

### 1. 安装 EPEL 仓库
Certbot 需要通过 EPEL 仓库安装，首先确保 EPEL 仓库已启用：

```bash
sudo yum install epel-release
```

---

### 2. 安装 Certbot 和 Nginx 插件
安装 `certbot` 和 `certbot-nginx` 插件：

```bash
sudo yum install certbot python3-certbot-nginx
```

---

### 3. 获取 SSL 证书
如果尚未获取 SSL 证书，可以使用以下命令为 Nginx 配置证书：

```bash
sudo certbot --nginx
```

按照提示输入域名和邮箱地址，Certbot 会自动配置 Nginx 并获取证书。

---

### 4. 测试自动续期
Certbot 默认会自动配置续期任务。你可以通过以下命令测试续期是否正常工作：

```bash
sudo certbot renew --dry-run
```

如果测试成功，说明自动续期配置正确。

---

### 5. 手动续期（可选）
如果需要立即续期证书，可以运行以下命令：

```bash
sudo certbot renew --force-renewal
```

---

### 6. 检查续期任务
Certbot 会自动创建一个定时任务来续期证书。你可以通过以下命令查看定时任务：

```bash
sudo systemctl list-timers
```

或者查看 Certbot 的日志文件：

```bash
sudo cat /var/log/letsencrypt/letsencrypt.log
```

---

### 7. 重启 Nginx（如果需要）
Certbot 通常会自动重启 Nginx 以加载新证书。如果需要手动重启 Nginx，可以运行：

```bash
sudo systemctl restart nginx
```

---

### 8. 防火墙配置（如果需要）
确保防火墙允许 HTTPS 流量（端口 443）：

```bash
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

---

### 总结
在 CentOS 7 上，Certbot 会自动配置 SSL 证书的续期任务。你只需确保 Certbot 和 Nginx 插件已安装，并定期检查续期任务是否正常运行即可。

### 异常
在 CentOS 7 上，`python3-certbot-nginx` 包可能不可用，因为 CentOS 7 默认使用 Python 2.x，而不是 Python 3.x。不过，你可以通过以下方法解决这个问题，并成功安装 Certbot 和 Nginx 插件。

---

### 解决方案：使用 `certbot-nginx`（Python 2.x 版本）

1. **安装 Certbot 和 Nginx 插件（Python 2.x 版本）**

   运行以下命令安装 Certbot 和 Nginx 插件：

   ```bash
   sudo yum install certbot python2-certbot-nginx
   ```

   如果 `python2-certbot-nginx` 也不可用，可以直接安装 `certbot`，然后手动配置 Nginx：

   ```bash
   sudo yum install certbot
   ```

2. **获取 SSL 证书**

   使用以下命令为 Nginx 配置 SSL 证书：

   ```bash
   sudo certbot --nginx
   ```

   如果 `certbot --nginx` 不可用（因为缺少 `python3-certbot-nginx`），可以使用以下命令手动获取证书：

   ```bash
   sudo certbot certonly --webroot -w /usr/share/nginx/html -d example.com
   ```

   其中：
   - `-w /usr/share/nginx/html` 是 Nginx 的默认根目录，确保替换为你的网站根目录。
   - `-d example.com` 是你的域名。

3. **手动配置 Nginx**

   如果使用 `certbot certonly` 获取证书，需要手动配置 Nginx 以使用 SSL 证书。编辑 Nginx 配置文件（通常位于 `/etc/nginx/nginx.conf` 或 `/etc/nginx/conf.d/your-site.conf`），添加以下内容：

   ```nginx
   server {
       listen 443 ssl;
       server_name example.com;

       ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
       ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

       root /usr/share/nginx/html;
       index index.html;
   }
   ```

   然后重启 Nginx：

   ```bash
   sudo systemctl restart nginx
   ```

4. **测试自动续期**

   运行以下命令测试自动续期是否正常工作：

   ```bash
   sudo certbot renew --dry-run
   ```

5. **设置自动续期**

   Certbot 会自动创建一个定时任务来续期证书。你可以通过以下命令查看定时任务：

   ```bash
   sudo systemctl list-timers
   ```

   或者手动检查 Certbot 的续期脚本：

   ```bash
   sudo cat /etc/cron.d/certbot
   ```

---

### 总结

在 CentOS 7 上，如果 `python3-certbot-nginx` 不可用，可以安装 `python2-certbot-nginx` 或直接使用 `certbot certonly` 手动获取证书并配置 Nginx。Certbot 的自动续期功能仍然可以正常工作。