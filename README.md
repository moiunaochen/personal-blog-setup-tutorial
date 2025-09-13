# 基于 WordPress 搭建个人博客教程

> 本教程适合零基础到中级用户，手把手教你从购买服务器、域名，到部署 WordPress 并上线个人博客。  

---

## 目录
- [1. 前言](#1-前言)
- [2. 准备工作](#2-准备工作)
- [3. 域名与服务器配置](#3-域名与服务器配置)
- [4. 安装基础环境](#4-安装基础环境)
- [5. 安装与配置 WordPress](#5-安装与配置-wordpress)
- [6. 配置 HTTPS](#6-配置-https)
- [7. WordPress 初始化](#7-wordpress-初始化)
- [8. 性能与安全优化](#8-性能与安全优化)
- [9. 自动化与版本管理（可选）](#9-自动化与版本管理可选)
- [10. 常见问题与解决方案](#10-常见问题与解决方案)
- [11. 总结与扩展](#11-总结与扩展)

---

## 1. 前言
- 最终效果展示（可附截图或演示链接）



WordPress 是全球最流行的开源博客与 CMS 系统，拥有丰富的主题和插件生态。  
本教程将带你完成：
- 购买并配置服务器与域名
- 安装 LNMP 环境
- 部署 WordPress
- 配置 HTTPS
- 优化性能与安全

## 2. 准备工作

### 服务器
- 推荐配置：**2 核 CPU / 2GB 内存 / 40GB SSD / 3Mbps 带宽**
- 推荐厂商：Oracle Cloud、Google Cloud、阿里云、京东云、雨云
- 系统建议：Ubuntu、Debian、Alibaba Cloud Linux  

这里新手学习可以选择阿里云68一年  
<img width="985" height="604" alt="image" src="https://github.com/user-attachments/assets/274f5b0a-4331-434b-ba38-80cfb31d7aa9" />
  
- 系统选择Alibaba Cloud Linux
- 预装软件选择宝塔面板

- 购买后到空控制台可以进行详细配置（可以在控制台重置安装的系统）
<img width="880" height="516" alt="image" src="https://github.com/user-attachments/assets/d7083497-fd6f-42d1-8f3c-d2e5b17caea6" />
    

### 开放端口  
确保服务器安全组开放： 
- 22（SSH） 必开
- 80（HTTP） 必开
- 443（HTTPS） 必开
- 3389（Windows远程登录）  
- 21 （FTP服务（命令控制）
- 20（FTP协议）

<img width="1442" height="635" alt="image" src="https://github.com/user-attachments/assets/e7b461fb-988b-4437-b7bf-38992795f896" />  
<img width="863" height="563" alt="image" src="https://github.com/user-attachments/assets/b0ce59cd-64cb-4cd0-b83e-0b45fc98dca4" />
<img width="1465" height="650" alt="image" src="https://github.com/user-attachments/assets/f301a58b-4e15-469b-b458-87567b3df19d" />  



### 设置密码  


### 安装宝塔面板  
![公网ip](image\image5.png)    
![安装宝塔](image\image6.png)  
![安装宝塔LNMP](image\image7.png)
### 域名





### 本地工具
- SSH 客户端：Windows 推荐 [Xshell](https://www.netsarang.com/en/xshell/)，Mac/Linux 直接使用终端
- 浏览器：Edge / Chrome

---

## 3. 域名与服务器配置

### 3.1 域名解析
1. 登录域名注册商控制台
2. 添加 **A 记录** 指向服务器公网 IP
3. 等待解析生效（通常 5 分钟 - 1 小时）

```bash
ping yourdomain.com
```

4. 安装基础环境
4.1 更新系统

sudo apt update && sudo apt upgrade -y

4.2 安装 LNMP 环境

sudo apt install nginx mysql-server php-fpm php-mysql php-cli php-curl php-xml php-mbstring unzip -y

4.3 创建数据库

sudo mysql -u root -p
CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'yourpassword';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;

5. 安装与配置 WordPress
5.1 下载 WordPress

cd /var/www/
sudo wget https://cn.wordpress.org/latest-zh_CN.zip
sudo unzip latest-zh_CN.zip
sudo mv wordpress blog

5.2 配置权限

sudo chown -R www-data:www-data /var/www/blog
sudo chmod -R 755 /var/www/blog

5.3 配置 Nginx

server {
    listen 80;
    server_name yourdomain.com;
    root /var/www/blog;

    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}

sudo ln -s /etc/nginx/sites-available/yourdomain.com /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

6. 配置 HTTPS
6.1 安装 Certbot

sudo apt install certbot python3-certbot-nginx -y

6.2 申请证书

sudo certbot --nginx -d yourdomain.com

6.3 自动续期

sudo systemctl enable certbot.timer

7. WordPress 初始化
浏览器访问 https://yourdomain.com

按向导填写：

数据库名：wordpress

用户名：wpuser

密码：yourpassword

设置站点标题、管理员账号

8. 性能与安全优化
安装缓存插件（如 WP Super Cache）

数据库定期优化

安装安全插件（如 Wordfence）

禁用 XML-RPC（防止暴力破解）

9. 自动化与版本管理（可选）
使用 Git 管理主题与插件

配置 GitHub Actions 自动部署

定期备份到云存储（OSS、S3）


10. 常见问题与解决方案
问题	可能原因	解决方案
数据库连接错误	配置错误	检查 wp-config.php
SSL 证书无效	证书过期	重新运行 certbot
WordPress 更新失败	权限不足	检查 www-data 权限
11. 总结与扩展
可接入 CDN（Cloudflare、阿里云 CDN）

使用对象存储（OSS/S3）存放媒体文件

搭配 CI/CD 实现自动化部署

作者：@你的GitHub用户名 许可证：MIT


---

我建议你下一步可以：
- 在每个步骤加上 **截图**（如宝塔面板安装界面、WordPress 安装向导）
- 在 GitHub README 顶部加 **效果图**，提升吸引力
- 如果你愿意，我可以帮你 **加一套 GitHub Actions 自动部署 WordPress 主题的工作流**，这样你的教程会更高级、自动化。  

你要我帮你加这个 **自动部署工作流** 吗？这样读者可以直接用 GitHub 管理 WordPress 主题和插件。
