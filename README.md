# 个人自用搭建x-ui代理面板并升级https协议方案（适用于Debian 12系统）

## 更新和升级系统
为了确保你的系统是最新的，执行以下命令来更新和升级软件包。

### 步骤 1：更新软件包列表

首先，使用 `sudo apt update`（或者 `sudo apt-get update`）来更新系统的软件包源列表。这一步会让你的系统知道有哪些包的版本更新。没有执行这一步，系统将不知道可用的更新版本。

```bash
sudo apt update
```

### 步骤 2：升级已安装的软件包

然后，运行 `sudo apt upgrade`（或者 `sudo apt-get upgrade`）来升级所有已安装的软件包到最新版本。它会下载并安装新版本的软件包，但不会移除现有包，也不会自动安装新的依赖包。

```bash
sudo apt upgrade
```

### 步骤 3：升级系统（可选）

如果在 `upgrade` 过程中遇到需要安装新依赖包，或者旧的软件包必须被删除才能升级（比如某些包需要替换），你可以选择使用 `sudo apt full-upgrade`（或者 `sudo apt-get dist-upgrade`），这会处理新依赖的安装、包的替换以及不再需要的包的删除。

```bash
sudo apt full-upgrade
```

### 步骤 4：清理系统（可选）

完成升级后，建议清理不再需要的包和缓存，以节省磁盘空间。你可以使用以下命令：

#### 删除不再需要的包（自动安装的，且不再使用的包）：

```bash
sudo apt autoremove
```

#### 清理下载的软件包缓存（这个步骤可以删除下载的 `.deb` 文件，释放空间）：

```bash
sudo apt clean
```

## 使用一键脚本检测VPS信息（可选）

### 融合怪测试脚本
此处鸣谢 @oneclickvirt

```bash
curl -L https://raw.githubusercontent.com/oneclickvirt/ecs/master/goecs.sh -o goecs.sh && chmod +x goecs.sh && bash goecs.sh env && bash goecs.sh install && goecs
```

### 流媒体解锁检测

```bash
bash <(curl -L -s media.ispvps.com)
```

## 安装并启用防火墙（可选）


```bash
sudo apt install ufw
```


```bash
sudo ufw allow 22
```

```bash
sudo ufw enable
```
## 安装并启用x-ui
此处鸣谢 @alireza0

```bash
bash <(curl -Ls https://raw.githubusercontent.com/alireza0/x-ui/master/install.sh)
```

## 安装并启用Nginx

### 步骤 1：更新软件包索引

```bash
sudo apt update
```

### 步骤 2：安装 Nginx

```bash
sudo apt install nginx
```

### 步骤 3：启动 Nginx 服务
启用 Nginx 在启动时自动启动

```bash
sudo systemctl enable nginx
```

启动 Nginx 服务

```bash
sudo systemctl start nginx
```

### 步骤 4：验证 Nginx 是否成功运行

```bash
sudo systemctl status nginx
```

### 步骤 5：配置防火墙（如果启用了 UFW）
如果你使用 UFW 作为防火墙，你需要允许 HTTP 和 HTTPS 流量。运行以下命令：

```bash
sudo ufw allow 'Nginx Full'
```

### 步骤 6：配置 Nginx（可选）（在申请SSL证书后进行此操作）
Nginx 默认配置文件位于 /etc/nginx/nginx.conf，网站的配置文件通常放在 /etc/nginx/sites-available/ 目录中，并通过符号链接到 /etc/nginx/sites-enabled/ 来启用。

生成指定域名配置文件

```bash
sudo nano /etc/nginx/sites-available/
```

Nginx 配置示例：

```bash
# 重定向 HTTP 到 HTTPS
server {
    listen 80;
    server_name ; #此处填写部署的子域名

    # 所有请求从 HTTP 重定向到 HTTPS
    return 301 https://$host$request_uri;
}

# HTTPS 配置
server {
    listen 443 ssl http2;
    server_name ; #此处填写部署的子域名

    # SSL 证书和私钥文件路径
    ssl_certificate ;
    ssl_certificate_key ;

    # SSL 配置优化
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # SSL 缓存设置（可选）
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # HSTS (HTTP Strict Transport Security)
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;

    # 代理到后端 x-ui 服务的配置
    location / {
        proxy_pass http://localhost:;  # 后端服务的地址和端口
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket 相关配置，如果你的服务使用 WebSocket
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    # 记录访问日志和错误日志
    access_log /var/log/nginx/x-ui_access.log;
    error_log /var/log/nginx/x-ui_error.log;
}
```

保存修改并关闭编辑器。对于 nano，按 CTRL + O 保存，然后按 CTRL + X 退出。

创建符号链接
使用 ln -s 命令将配置文件从 sites-available 目录链接到 sites-enabled 目录

```bash
sudo ln -s /etc/nginx/sites-available/my_site /etc/nginx/sites-enabled/
```

### 步骤 7：检查 Nginx 配置文件的正确性

```bash
sudo nginx -t
```

### 步骤 8：重新加载 Nginx 配置

```bash
sudo systemctl reload nginx
```

## 通过Cloudflare DNS验证申请SSL证书并配置自动更新

### 步骤 1：获取 Cloudflare API Token
首先，您需要从 Cloudflare 获取一个 API token，该 token 允许 Certbot 使用 Cloudflare 的 DNS 来完成 DNS 验证。

登录到 Cloudflare Dashboard。
进入 "My Profile"（个人资料） > API Tokens。
点击 "Create Token"。
在模板中选择 "Zone DNS"（Zone DNS 编辑权限）。
为您的域名创建一个 API Token，确保它有足够的权限来管理 DNS 记录。
保存生成的 API Token。

### 步骤 2：安装 Certbot 和 Cloudflare 插件

```bash
sudo apt update
sudo apt install certbot python3-certbot-dns-cloudflare
```

### 步骤 3：配置 Cloudflare API Token
为了让 Certbot 使用 Cloudflare API 来进行 DNS 验证，您需要创建一个配置文件来存储 API Token。假设您将该文件保存在 /etc/letsencrypt/cloudflare.ini。

#### 创建配置文件 cloudflare.ini 

```bash
sudo nano /etc/letsencrypt/cloudflare.ini
```

#### 将以下内容添加到文件中

```bash
dns_cloudflare_api_token = YOUR_CLOUDFLARE_API_TOKEN
```

保存修改并关闭编辑器。对于 nano，按 CTRL + O 保存，然后按 CTRL + X 退出。

#### 修改文件权限以确保安全

```bash
sudo chmod 600 /etc/letsencrypt/cloudflare.ini
```

### 步骤 4： 申请 SSL 证书
使用 Certbot 来申请 SSL 证书，并通过 Cloudflare DNS 验证。

##### 运行以下命令来申请证书：
请将 example.com 替换为您的域名。此命令会自动使用 Cloudflare 的 DNS API 来完成 DNS 记录的创建和验证。

```bash
sudo certbot certonly \
    --dns-cloudflare \
    --dns-cloudflare-credentials /etc/letsencrypt/cloudflare.ini \
    -d example.com -d "*.example.com" \
    --agree-tos \
    --email yourname@domain.com \
    --non-interactive \
    -v
```
注意此处需要修改三处变量：根域名，通配符域名和Let’s Encrypt注册邮箱

运行完指令后等待申请完成。注意证书和密钥保存位置提示

Certificate is saved at: 

Key is saved at:         

### 步骤 5：配置自动续期
Certbot 默认会自动配置证书的续期，但是您需要确保 Certbot 正确设置了自动续期任务。

#### 验证 Certbot 自动续期是否正常工作
Certbot 会创建一个 cron 任务或 systemd 服务来自动续期证书。您可以检查续期配置

```bash
sudo certbot renew --dry-run
```

#### 添加 Cron 任务（如果需要）
如果系统没有自动添加续期任务，您可以手动添加一个 cron 任务，每天检查证书并在必要时续期。

编辑

```bash
sudo crontab -e
```

添加以下行以确保证书每月自动续期

```bash
0 0 * * * certbot renew --quiet && systemctl reload nginx
```

保存修改并关闭编辑器。对于 nano，按 CTRL + O 保存，然后按 CTRL + X 退出。
