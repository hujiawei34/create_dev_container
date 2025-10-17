本项目指导如何使用docker/podman自寻建一个自己开发的容器镜像，并上传到阿里云公共仓库，后续其他电脑或者同事可以直接pull 你的镜像，快速搭建开发环境
按照以下步骤执行即可：
# 提交image
先登录阿里云,搜索'容器镜像服务',创建自己的个人版实例,
```bash
sudo docker login --username=hujiawei2727 crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com

docker tag aa291681f325 crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/osteoporosis-platform:v1
# aa291681f325 :docker image id
# crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com 个人实例访问地址
# hjw2727: 个人实例空间
# osteoporosis-platform : image name
# v1 : image version
docker push crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/osteoporosis-platform:v1
```

# windows机器
推荐windows使用wsl,[win10安装wsl文档](docs/install_wsl_in_win10.md)
wsl中安装podman见[ubuntu_podman_install_guide.md](docs/ubuntu_podman_install_guide.md)
以下命令在wsl中执行
```bash
podman pull crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/dev_container
```
# ubuntu机器
```bash
docker pull crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/dev_container
docker tag crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/dev_container dev_container
sudo docker pull swr.cn-east-3.myhuaweicloud.com/hujiawei2727/dev_container
```
