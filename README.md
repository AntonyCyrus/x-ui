# 个人自用搭建x-ui代理面板方案（适用于Debian 12系统）

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

## 安装并启用Nginx

### 更新软件包索引

```bash
sudo apt update
```

### 安装 Nginx

```bash
sudo apt install nginx
```

### 启动 Nginx 服务
启用 Nginx 在启动时自动启动

```bash
sudo systemctl enable nginx
```

启动 Nginx 服务

```bash
sudo systemctl start nginx
```

### 验证 Nginx 是否成功运行

```bash
sudo systemctl status nginx
```

### 配置防火墙（如果启用了 UFW）
如果你使用 UFW 作为防火墙，你需要允许 HTTP 和 HTTPS 流量。运行以下命令：

```bash
sudo ufw allow 'Nginx Full'
```

### 配置 Nginx（可选）
Nginx 默认配置文件位于 /etc/nginx/nginx.conf，网站的配置文件通常放在 /etc/nginx/sites-available/ 目录中，并通过符号链接到 /etc/nginx/sites-enabled/ 来启用。

### 检查 Nginx 配置文件的正确性

```bash
sudo nginx -t
```

### 重新加载 Nginx 配置

```bash
sudo systemctl reload nginx
```
