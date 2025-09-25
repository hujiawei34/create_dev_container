# Ubuntu安装Podman完整指导

## 1. 配置Ubuntu APT源（推荐）

在安装Podman之前，建议先配置国内APT源以提升下载速度。

### 1.1 检查Ubuntu版本
```bash
lsb_release -c
```

### 1.2 备份原始源列表
```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
```

### 1.3 配置阿里云APT源
```bash
# 编辑源配置文件
sudo nano /etc/apt/sources.list
```

根据您的Ubuntu版本，替换为相应的阿里云源：

**Ubuntu 22.04 LTS (jammy)**：
```bash
deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
```


### 1.4 更新软件包列表
```bash
sudo apt update
```

## 2. 安装Podman

### 方法一：通过apt安装（推荐）
```bash
# 更新包列表
sudo apt update

# 安装podman
sudo apt install podman

# 验证安装
podman --version
```


## 3. 配置podman阿里云镜像源

### 3.1 备份原始配置（如果存在）
```bash
sudo cp /etc/containers/registries.conf /etc/containers/registries.conf.backup 2>/dev/null || true
```

### 3.2 创建新的配置文件
```bash
# 创建配置目录（如果不存在）
sudo mkdir -p /etc/containers

# 编辑配置文件
sudo nano /etc/containers/registries.conf
```

### 3.3 配置内容
见[registries.conf](../config/registries.conf)

## 4. 验证配置

```bash
# 测试拉取镜像
sudo podman pull hello-world

# 查看配置信息
podman info | grep -A 10 registries

# 测试其他常用镜像
podman pull nginx
podman pull ubuntu:22.04
```

## 5. 安装后配置（可选）

### 替换docker命令,并开启自动补全
```bash
update-alternatives --install /usr/bin/docker docker /usr/bin/podman 100
ln -s /usr/share/bash-completion/completions/podman /etc/bash_completion.d/podman
. /etc/profile.d/bash_completion.sh 
```

## 6. 常见问题及解决方案

### 6.1 配置格式错误
**错误信息**：`mixing sysregistry v1/v2 is not supported`

**解决方案**：
- 确保使用纯 v2 格式配置
- 使用 `unqualified-search-registries` 而不是 `[registries.search]`
- 避免混合 v1 和 v2 语法


## 8. 测试命令

```bash
# 基本测试
podman run hello-world

# 运行交互式容器
podman run -it ubuntu:22.04 /bin/bash

# 后台运行服务
podman run -d -p 8080:80 nginx

# 查看运行的容器
podman ps

# 停止容器
podman stop <container_id>
```

## 9. 故障排除

### 9.1 清除配置重新开始
```bash
# 删除配置文件
sudo rm /etc/containers/registries.conf

# 重新安装podman
sudo apt remove podman
sudo apt install podman
```

### 9.2 查看详细日志
```bash
# 查看详细拉取日志
podman pull --log-level=debug <image_name>

# 查看系统信息
podman system info
```

### 9.3 恢复备份配置
```bash
# 如果有备份，可以恢复
sudo cp /etc/containers/registries.conf.backup /etc/containers/registries.conf
```

配置完成后，Podman将优先使用阿里云镜像源，大大提升容器镜像的下载速度。