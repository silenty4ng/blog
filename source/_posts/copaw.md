---
title: 在服务器使用 Docker 部署 CoPaw
categories: 散文杂记
tags: 
    - ai
    - copaw
date: 2026/3/11 22:00
updated: 2026/3/11 22:00
---

## 安装 Docker
```
apt install docker.io
```

## 运行 CoPaw 容器
```
docker run --name copaw --restart=always -d --network=host -v ./copaw-data:/app/working -v ./copaw-secrets:/app/working.secret agentscope/copaw:latest
```

## 进行初始化配置（工作区文件）
```
docker exec -it copaw bash
copaw init --defaults
```

## 连接到 Web 控制台
```
ssh -N -L 8088:127.0.0.1:8088 root@ip
```
访问 http://127.0.0.1:8088 配置模型及频道。