# 个人自用搭建x-ui代理面板方案

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

### VPS检测

```bash
bash <(wget -qO- bash.spiritlhl.net/ecs)
```

### 三网回程路由测试直观版

```bash
curl https://raw.githubusercontent.com/oneclickvirt/backtrace/main/backtrace_install.sh -sSf | bash && backtrace
```

### 流媒体解锁检测

```bash
bash <(curl -L -s media.ispvps.com)
```
