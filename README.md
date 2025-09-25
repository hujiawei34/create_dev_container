本项目指导如何使用docker/podman自寻建一个自己开发的容器镜像，并上传到阿里云公共仓库，后续其他电脑或者同事可以直接pull 你的镜像，快速搭建开发环境
按照以下步骤执行即可：
# windows机器
推荐windows使用wsl,安装wsl文档见**待补充**
wsl中安装podman见[ubuntu_podman_install_guide.md](docs/ubuntu_podman_install_guide.md)
以下命令在wsl中执行
```bash
podman pull crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/dev_container
```
# ubuntu机器
```bash
docker pull crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/dev_container
docker tag crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/dev_container dev_container
```