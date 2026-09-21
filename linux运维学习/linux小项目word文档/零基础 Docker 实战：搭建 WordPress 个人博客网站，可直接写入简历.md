# Docker Compose 实战笔记：搭建 WordPress 个人博客

## 一、 核心概念（先搞懂“是什么”）

### 📌 核心知识点

- **Docker**：底层容器引擎。一个“便携密封便当盒”，打包了运行环境和依赖，一次只能管理一个容器。
    
- **Docker Compose**：官方编排工具。一个“组装说明书（YAML文件）”，用一条命令统一管理多个需要配合运行的容器。
    
- **YAML 文件**：绝不能用 Tab 键缩进，只能用 **2 个空格**。冒号代表键值对（冒号后必须加空格，如 `image: mysql:8.0`，但镜像名内部冒号不能加空格）。
    
- **数据卷（Volumes）**：双向隧道，把容器内数据映射到宿主机。
    
- **服务发现**：Docker 内部网络可通过服务名（如 `db`）直接连接，不需要写具体 IP。
    

## 二、 实战步骤

### 🛠️ 标准流程

1. **创建工作目录**：`mkdir -p ~/wordpress && cd ~/wordpress`
    
2. **编写 YAML 文件**：`vi docker-compose.yml`
    
    - **注意**：第一行 `version: "3"` 现已过时，建议删掉以避免警告。
        
    - **核心配置结构**：
        
    
    yaml
    
    services:
      db:
        image: mysql:8.0
        volumes:
          - ./mysql_data:/var/lib/mysql
        restart: always
        environment:
          MYSQL_ROOT_PASSWORD: 12345678
          MYSQL_DATABASE: wordpress
          MYSQL_USER: wpuser
          MYSQL_PASSWORD: 123456
      wordpress:
        image: wordpress:latest
        ports:
          - "8090:80"
        volumes:
          - ./wp_html:/var/www/html
        restart: always
        depends_on:
          - db
        environment:
          WORDPRESS_DB_HOST: db:3306
          WORDPRESS_DB_USER: wpuser
          WORDPRESS_DB_PASSWORD: 123456
          WORDPRESS_DB_NAME: wordpress
    
3. **语法检查**：`docker compose config`（不报错即代表语法无误）。
    
4. **启动项目**：`sudo docker compose up -d`
    
5. **查看状态**：`sudo docker compose ps`（两个容器必须都是 `Up`）。
    
6. **浏览器访问**：`http://虚拟机IP:8090`，填写站点标题、用户名（不叫 `admin`）、密码和邮箱，初始化博客。
    

## 三、 🚨 踩坑与高光记录（你的血泪史）

1. **权限拒绝（Permission Denied）**：
    
    - `docker compose up -d` 报权限错误（普通用户无权访问 `/var/run/docker.sock`）。解决办法：执行 `sudo usermod -aG docker $USER` 并 `newgrp docker`（或退出重连）。
        
    - `sudo: docker：找不到命令`：因 Docker 安装在非标准路径，使用 `sudo which docker` 找到真实路径并做软链接到 `/usr/bin/docker`。
        
2. **网络受限（拉取镜像超时）**：
    
    - 报错 `connection refused` 或进度条卡死。解决办法：修改 `/etc/docker/daemon.json`，配置国内可用的镜像加速器（如 `docker.1ms.run`、`docker.m.daocloud.io`）。**切记备份原有配置，修改后执行 `systemctl daemon-reload && systemctl restart docker`**。
        
3. **YAML 语法错误**：
    
    - 少冒号、层级不对、混用 Tab 都会导致解析失败。**务必用 `docker compose config` 校验，绝不闭着眼睛跑 `up -d`**。
        
4. **数据持久化验证（高光时刻）**：
    
    - 在博客写完文章后，执行 `docker compose down` 销毁容器，再执行 `docker compose up -d` 重新拉起。文章依然存在！这证明了 `volumes:` 挂载配置生效。
        
5. **外网访问限制（运维认知）**：
    
    - 目前只能局域网（宿主机）访问。真正让外人访问需要内网穿透（如 frp、ZeroTier），或购买云服务器+域名+备案+Nginx反向代理。
        

## 四、 面试考点与简历包装

### 💡 简历项目经验（直接复制）

> **项目：基于 Docker Compose 的 WordPress 个人博客容器化部署**
> 
> - 使用 Docker 容器化技术替代传统 LNMP 繁琐编译安装，实现了环境隔离与一键交付。
>     
> - 编写 `docker-compose.yml` 编排文件，实现 MySQL 与 WordPress 双容器协同部署、网络互通与开机自启。
>     
> - 配置数据卷（Volumes）实现数据库与网页文件的持久化，验证容器销毁重建后数据不丢失。
>     
> - 解决镜像拉取超时、Docker 用户组权限、YAML 语法踩坑等底层问题。
>     

### 💡 面试预演

- **面试官**：“Docker 和 Docker Compose 的区别是什么？”
    
- **你**：“Docker 是底层引擎，管理单个容器；Docker Compose 是编排工具，通过 YAML 文件定义和运行多个相关联的容器。在这个项目中，我用 Compose 一键拉起 MySQL 和 WordPress，并利用服务发现直接让 WordPress 通过 `db:3306` 连接数据库，免去了写死 IP 的麻烦。”
    

## 五、 运维进阶规划（下一步）

1. **Nginx 反向代理**：去掉 `8090` 端口，通过 Nginx 代理转发，让浏览器直接用 `80` 访问。
    
2. **HTTPS 证书**：申请 SSL 证书，配置 HTTPS 加密传输。
    
3. **CI/CD 流水线**：把 `docker-compose.yml` 放到 Git 仓库，通过 Jenkins 或 GitLab CI 自动部署。