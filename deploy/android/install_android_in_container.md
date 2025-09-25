本文档指导如何在开发的容器中安装android command_line 34版本 和gradle
# 准备开发容器环境
```bash
docker pull crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/ble_simulator
docker tag crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/ble_simulator localhost/ble_simulator
podman run -it --name ble_simulator localhost/ble_simulator /bin/bash
podman exec -it ble_simulator bash
```
以下命令都是在进入容器之后 执行
# 安装 jdk 17
```bash
apt install openjdk-17-jdk unzip wget less
javac --version
```
# 安装android command_line

```bash
echo "ANDROID_SDK_ROOT=/opt/android-sdk" >> ~/.bashrc
echo "PATH ${PATH}:${ANDROID_SDK_ROOT}/cmdline-tools/bin:${ANDROID_SDK_ROOT}/platform-tools" >> ~/.bashrc
source ~/.bashrc

export GRADLE_VERSION=8.4
export ANDROID_API_LEVEL=34
export ANDROID_BUILD_TOOLS_VERSION=34.0.0

mkdir -p ${ANDROID_SDK_ROOT}
cd ${ANDROID_SDK_ROOT}
wget -O commandlinetools.zip https://dl.google.com/android/repository/commandlinetools-linux-8092744_latest.zip
unzip commandlinetools.zip
rm commandlinetools.zip

yes | sdkmanager --sdk_root=${ANDROID_SDK_ROOT} --licenses
sdkmanager --sdk_root=${ANDROID_SDK_ROOT} "platform-tools" "platforms;android-${ANDROID_API_LEVEL}" "build-tools;${ANDROID_BUILD_TOOLS_VERSION}"

#check install result 
sdkmanager --sdk_root=${ANDROID_SDK_ROOT} --list_installed
```
# 安装 gradle
```bash
mkdir /opt/gradle
export GRADLE_VERSION=8.4
wget -O gradle.zip https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip
unzip gradle-${GRADLE_VERSION}-bin.zip
rm gradle-${GRADLE_VERSION}-bin.zip
update-alternatives --install /usr/bin/gradle gradle /opt/gradle/gradle-8.4/bin/gradle 100
gradle --version
mkdir -p ~/.gradle && \
    echo "org.gradle.jvmargs=-Xmx2048m -Dfile.encoding=UTF-8" >> ~/.gradle/gradle.properties && \
    echo "android.useAndroidX=true" >> ~/.gradle/gradle.properties && \
    echo "android.enableJetifier=true" >> ~/.gradle/gradle.properties
```
# 上传开发容器环境
```bash
podman commit ble_simulator dev_container:latest
podman login --username=hujiawei2727 crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com
podman tag localhost/dev_container crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/dev_container:latest
podman push crpi-qqo05szhq6ruqonk.cn-hangzhou.personal.cr.aliyuncs.com/hjw2727/dev_container:latest
```