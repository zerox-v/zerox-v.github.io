---
title: Centos7 使用阿里云镜像
date: 2025-07-09 17:10
tags: 
- Centos 7
- 阿里云镜像
categories:
- Centos 7
- 阿里云镜像
---

#### 备份镜像

```bash
 mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
```
#### 下载镜像

```bash
sudo curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
```

#### 刷新缓存

```bash
yum clean all # 清除旧缓存
yum makecache # 生成新缓存
```
