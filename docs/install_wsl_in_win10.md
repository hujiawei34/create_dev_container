[toc]

# 前言

使用windows开发，安装多个python和java版本是个头疼的事情，想要docker这种应用隔离环境，可以使用WSL2+DOCKER方式，本文档时候介绍如何在win10 LTSC版本中安装WSL2+docker

# 准备工作

WSL2版本需要win10 LTSC版本高于19041 及更高版本

检查当前windows版本信息

win+r 后输入`winver`,弹窗有windows版本信息

![image-20250925145649940](https://www.gkrynj.com/miniprogram-cdn/image/female/assets/pics/image-20250925145649940.png)



如果低于这个版本，自行下载新版本：

[MSDN系统库－致力于原版windows生态服务](https://www.xitongku.com/index.html)

下载win10 LTSC 21年版本可以满足要求，

装载ISO后，运行setup.sh

升级win10

**注意升级后系统 需要重新激活**

多次重启后，重新激活win10后，下面就可以安装WSL2了

# 安装 WSL2

参考文档：

[安装 WSL | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/install)

管理员启动powershell

```bash
wsl --install
```

然后重启电脑

# 安装ubuntu

按照文档中执行wsl --install -d Ubuntu会出现这个错误：0x80072ee7，可能是因为网络问题。

解决办法，直接众商店网页中下载

[ubuntu - Microsoft Apps](https://apps.microsoft.com/search?query=ubuntu&hl=zh-CN&gl=CN)

下载后运行，安装结束后会有个命令行窗口，让输入ubuntu的用户及密码，设置完成后，就进入ubuntu的命令行窗口了，

开始菜单中也增加了个ubuntu应用，后续可以直接进系统

注意，默认安装的ubuntu系统在

c:/Users/hjw/AppData/Local/Packages/CanonicalGroupLimited.Ubuntu22.04LTS_79rhkp1fndgsc 

在 Windows 系统中，WSL2 中的 Ubuntu 系统文件（包括其根目录）实际存储在 Windows 的一个虚拟硬盘文件（.vhdx）中，具体路径如下：

默认路径通常为：

```cmd
C:\Users\<你的Windows用户名>\AppData\Local\Packages\<Ubuntu发行版对应的包名>\LocalState\ext4.vhdx
```


其中：

<你的Windows用户名> 是你登录 Windows 时使用的用户名
<Ubuntu发行版对应的包名> 因具体的 Ubuntu 版本而异，通常类似 CanonicalGroupLimited.Ubuntu20.04LTS_79rhkp1fndgsc 这样的格式

按照以下步骤将vhdx文件挪走，避免占用c盘空间

## 修改vhdx文件 位置

要修改WSL2中Ubuntu系统（即其虚拟硬盘文件`.vhdx`）的存储位置，可以按照以下步骤操作：

### 步骤1：关闭所有WSL实例
首先确保WSL2中的Ubuntu已完全关闭，在Windows命令提示符或PowerShell中执行：
```powershell
wsl --shutdown
```

### 步骤2：导出Ubuntu系统镜像
将当前的Ubuntu系统导出为一个tar文件（备份），例如导出到`D:\wsl-backup\ubuntu.tar`：
```powershell
wsl --export Ubuntu-20.04 D:\wsl-backup\ubuntu.tar
```
（注意：`Ubuntu-20.04`是你的发行版名称，可通过`wsl --list`查看）

### 步骤3：注销当前的Ubuntu发行版
移除原来的Ubuntu实例（仅移除注册信息，不删除导出的tar文件）：
```powershell
wsl --unregister Ubuntu-20.04
```

### 步骤4：重新导入并指定新位置
将备份的tar文件导入到新的存储位置（例如`D:\wsl\ubuntu`）：
```powershell
wsl --import Ubuntu-20.04 D:\wsl\ubuntu D:\wsl-backup\ubuntu.tar --version 2
```
- 第一个路径`D:\wsl\ubuntu`是新的安装目录
- 第二个路径是之前导出的tar文件路径

### 步骤5：设置默认用户（可选）
重新导入后可能默认以root用户登录，需设置回原来的默认用户：
```powershell
ubuntu2004 config --default-user 你的用户名
```
（不同版本的Ubuntu可能需要调整命令，如`ubuntu config`或`ubuntu2204 config`）

### 完成后：
- 原来的`.vhdx`文件会被自动迁移到新位置
- 可删除原来的备份tar文件（如果确认迁移成功）

这种方法安全可靠，不会损坏系统文件，适用于需要将WSL2系统迁移到其他磁盘（如从C盘移到D盘）的场景。

## 创建不同实例

后续想要创建不同wsk实例，可以执行以下命令

```powershell
 wsl --import ubuntu-test D:\software\wsl\test-ubuntu D:\software\wsl\ubuntu.tar --version 2
 wsl -d ubuntu-test
 # 删除这个wsl，注意这个命令会清理这个wsl中的文件
 wsl --unregister ubuntu-test
```

## 使用wsl

### wsl中访问windows文件 

```bash
root@DESKTOP-H75TK46:/code# ll /mnt
total 8
drwxr-xr-x  7 root root 4096 Sep  1 21:26 ./
drwxr-xr-x 21 root root 4096 Sep  2 10:28 ../
drwxrwxrwx  1 root root 4096 Sep  1 21:24 c/
drwxrwxrwx  1 root root  512 Sep  2 09:31 d/
drwxrwxrwx  1 root root 4096 Sep  2 09:31 e/
drwxrwxrwt  4 root root  100 Sep  2 10:12 wsl/
drwxrwxrwt  7 root root  300 Sep  2 10:12 wslg/
```

在/mnt目录下有挂载的各个硬盘

### windows访问wsl文件 

一、方式一：通过 Windows 文件管理器直接访问（推荐临时使用）
WSL 会将 Linux 系统的文件目录 映射为 Windows 中的一个 “网络位置”，可以直接在文件管理器中打开，无需配置。
操作步骤：
打开 Windows 文件管理器（快捷键 Win + E）。
在左侧导航栏找到「此电脑」，双击进入后，在地址栏输入以下路径并按回车：

```cmd
\\wsl$
```

此时会显示你已安装的所有 WSL 实例（比如 Ubuntu-22.04、ubuntu-test），双击某个实例进入。
即可看到 Linux 系统的完整目录（如 /home、/root、/etc 等），后续操作与访问 Windows 本地文件完全一致：
可以直接打开、编辑 Linux 内的文件（如用记事本、VS Code 打开 /home/yourname/test.py）。
可以复制 Linux 内的文件到 Windows 本地（如将 /var/log/syslog 复制到 D:\备份）。
可以将 Windows 本地文件复制到 Linux 内（如将 D:\代码\demo.sh 复制到 /home/yourname）。
二、方式二：通过 WSL 命令获取具体路径（推荐精准访问）
如果知道 WSL 内文件的具体路径（比如 /home/yourname/project），可以通过 WSL 命令直接生成 Windows 可访问的路径，避免手动输入。
操作步骤：
打开 WSL 终端（如 Ubuntu 终端）。
进入你想访问的 Linux 目录（比如 cd /home/yourname/project）。
执行以下命令，将当前 Linux 目录 转换为 Windows 路径 并复制到剪贴板：
bash

方法2：自动复制到 Windows 剪贴板（需安装 xclip，适合 Ubuntu/Debian）

wslpath -w "$PWD" | clip.exe

# 安装podman

```bash
apt install podman
```



# ~~安装 docker~~

后面发现使用podman更方便快速,不推荐在wsl中安装docker了,因为:

1. docker destop要独立运行,否刚wsl中的ubuntu docker命令不可用
2. 安装麻烦,见下面这么多步骤,而podman安装一行命令就行



因为直接在wsl的ubuntu中安装docker-ce，docker服务无法启动，所以按照官方文档[Get started with Docker containers on WSL | Microsoft Learn](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-containers)安装 docker desktop

下载->安装后，打开提示wsl --update，说明wsl版本不对，要更新

打开powershell，输入wsl --update时提示 windows要更新，还要将升级后的win10更新各种补丁，注意更新前要将这个设置打开 

![image-20250902095907108](https://www.gkrynj.com/miniprogram-cdn/image/female/assets/pics/image-20250902095907108.png)

![image-20250902095917931](https://www.gkrynj.com/miniprogram-cdn/image/female/assets/pics/image-20250902095917931.png)



更新重启后，再打开docker desktop时，已经可以首页了，打开 settings，检查配置如下 ：

![image-20250902100056805](https://www.gkrynj.com/miniprogram-cdn/image/female/assets/pics/image-20250902100056805.png)

![image-20250902100140660](https://www.gkrynj.com/miniprogram-cdn/image/female/assets/pics/image-20250902100140660.png)

![image-20250902100246397](https://www.gkrynj.com/miniprogram-cdn/image/female/assets/pics/image-20250902100246397.png)

![image-20250902100304538](https://www.gkrynj.com/miniprogram-cdn/image/female/assets/pics/image-20250902100304538.png)

配置国内docker 源

```json
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "registry-mirrors": [
    "https://docker.registry.cyou",
    "https://docker-cf.registry.cyou",
    "https://dockercf.jsdelivr.fyi",
    "https://docker.jsdelivr.fyi",
    "https://dockertest.jsdelivr.fyi",
    "https://mirror.aliyuncs.com",
    "https://dockerproxy.com",
    "https://mirror.baidubce.com",
    "https://docker.m.daocloud.io",
    "https://docker.nju.edu.cn",
    "https://docker.mirrors.sjtug.sjtu.edu.cn",
    "https://docker.mirrors.ustc.edu.cn",
    "https://mirror.iscas.ac.cn",
    "https://docker.rainbond.cc"
  ]
}
```

点击apply，应用后，打开ubuntu wsl，就可以使用docker开发了！

# 拉取image

推荐两个常用image

```bash
docker pull maven:3.8-openjdk-17
docker pull python
```

# 安装claude

### 安装node

```bash
# Download and install nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

# in lieu of restarting the shell
\. "$HOME/.nvm/nvm.sh"

# Download and install Node.js:
nvm install 22

# Verify the Node.js version:
node -v # Should print "v22.19.0".
nvm current # Should print "v22.19.0".

# Verify npm version:
npm -v # Should print "10.9.3".

```

### 安装claude

```bash
npm install -g https://gaccode.com/claudecode/install --registry=https://registry.npmmirror.com
```

