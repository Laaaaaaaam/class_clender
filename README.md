# Android APK 打包说明

## 环境要求

打包Android APK需要在 **Linux系统**（推荐Ubuntu 20.04+）或 **WSL2** 中进行。

## 方法一：使用WSL2（Windows用户推荐）

### 1. 安装WSL2
```powershell
wsl --install -d Ubuntu-22.04
```

### 2. 进入WSL2并安装依赖
```bash
sudo apt update
sudo apt install -y git zip unzip openjdk-17-jdk python3-pip autoconf libtool pkg-config libncurses5-dev libncursesw5-dev libtinfo5 cmake libffi-dev libssl-dev automake

pip3 install --user buildozer cython==0.29.33
```

### 3. 将项目复制到WSL
```bash
# 在WSL中，Windows的C盘挂载在 /mnt/c
cp -r /mnt/c/Users/Administrator/Desktop/class_a/apk ~/class_reminder
cd ~/class_reminder
```

### 4. 打包APK
```bash
# 首次打包（会自动下载SDK/NDK，需要较长时间）
buildozer android debug

# 打包完成后，APK位于 bin/ 目录
```

## 方法二：使用Linux系统

### 1. 安装依赖
```bash
sudo apt update
sudo apt install -y git zip unzip openjdk-17-jdk python3-pip autoconf libtool pkg-config libncurses5-dev libncursesw5-dev libtinfo5 cmake libffi-dev libssl-dev automake

pip3 install --user buildozer cython==0.29.33
```

### 2. 进入项目目录并打包
```bash
cd apk
buildozer android debug
```

## 方法三：使用GitHub Actions（无需Linux环境）

### 1. 创建GitHub仓库并上传代码

### 2. 创建 `.github/workflows/build.yml`:
```yaml
name: Build Android APK

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.10'
    
    - name: Install dependencies
      run: |
        pip install buildozer cython==0.29.33
    
    - name: Build APK
      run: |
        cd apk
        buildozer android debug
    
    - name: Upload APK
      uses: actions/upload-artifact@v3
      with:
        name: app-debug
        path: apk/bin/*.apk
```

### 3. 推送代码后，在Actions中下载APK

## 常用命令

```bash
# 调试版APK
buildozer android debug

# 发布版APK（需要签名）
buildozer android release

# 清理构建缓存
buildozer android clean

# 查看日志
buildozer android logcat
```

## 安装APK

将生成的APK传输到Android手机：
- 通过数据线复制到手机
- 通过微信/QQ文件传输
- 上传到云盘后下载

在手机上打开APK文件即可安装。

## 注意事项

1. 首次打包需要下载Android SDK/NDK，耗时约30-60分钟
2. 确保手机已开启"允许安装未知来源应用"
3. 如需修改应用图标，将图标文件命名为 `icon.png` 放在apk目录
4. 修改 `buildozer.spec` 中的 `version` 字段可更新版本号
