# 在xavier中配置Kinect DK环境
kinect dk版本以1.4为例

## 一 给xavier更换软件源
https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/

1. 备份之前的sources.list

   `sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup`

2. 修改sources.list

   `sudo gedit /etc/apt/sources.list`

   Xavier清华的(此版本为ubunut18.04,如果是其他版本则需要更换):

    ```
    # 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse`

    # 预发布软件源，不建议启用
    # deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
    # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
    ```
  

3. 更新

   `sudo apt-get update && sudo apt-get upgrade`
   
## 二 下载 kinect DK 源码 并且完成依赖安装
1. 下载源码
    `git clone https://github.com/microsoft/Azure-Kinect-Sensor-SDK.git`
2. 安装依赖
    在~/Azure-Kinect-Sensor-SDK/scripts/docker 路径下找到setup-ubuntu.sh文件，使用gedit打开
    
    原文为：
    ```
    #!/bin/bash

    # Usage:
    # ./setup-ubuntu.sh [arm64 | amd64]

    # Warning! This will override your sources.list file!!

    arch=amd64

    # Copy off old sources.list file
    cp /etc/apt/sources.list /etc/apt/sources.list.old
    echo "Backed up /etc/apt/sources.list to /etc/apt/sources.list.old"

    # Copy over the new file
    cp sources.list /etc/apt/sources.list
    echo "Overwrote /etc/apt/sources.list with sources.list"

    apt-get update

    apt-get install wget -y

    # Add Public microsoft repo keys to the image
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb
    dpkg -i packages-microsoft-prod.deb

    if [ "$1" = "arm64" ]; then
        arch="arm64"
    fi

    echo "Setting up for building $arch binaries"

    dpkg --add-architecture amd64
    dpkg --add-architecture arm64

    apt-get update

    packages=(\
        gcc-aarch64-linux-gnu \
        g++-aarch64-linux-gnu \
        file \
        dpkg-dev \
        qemu \
        binfmt-support \
        qemu-user-static \
        pkg-config \
        ninja-build \
        doxygen \
        clang \
        python3 \
        gcc \
        g++ \
        git \
        git-lfs \
        nasm \
        cmake \
        powershell \
        libgl1-mesa-dev:$arch \
        libsoundio-dev:$arch \
        libjpeg-dev:$arch \
        libvulkan-dev:$arch \
        libx11-dev:$arch \
        libxcursor-dev:$arch \
        libxinerama-dev:$arch \
        libxrandr-dev:$arch \
        libusb-1.0-0-dev:$arch \
        libssl-dev:$arch \
        libudev-dev:$arch \
        mesa-common-dev:$arch \
        uuid-dev:$arch )

    if [ "$arch" = "amd64" ]; then
        packages+=(libopencv-dev:$arch)
    fi

    apt-get install -y --no-install-recommends ${packages[@]}
    ```
    修改为：
    ```
    #!/bin/bash

    # Usage:
    # ./setup-ubuntu.sh [arm64 | amd64]

    # Warning! This will override your sources.list file!!

    arch=arm64

    # Copy off old sources.list file
    #cp /etc/apt/sources.list /etc/apt/sources.list.old
    #echo "Backed up /etc/apt/sources.list to /etc/apt/sources.list.old"

    # Copy over the new file
    #cp sources.list /etc/apt/sources.list
    #echo "Overwrote /etc/apt/sources.list with sources.list"

    apt-get update

    apt-get install wget -y

    # Add Public microsoft repo keys to the image
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb
    dpkg -i packages-microsoft-prod.deb

    if [ "$1" = "arm64" ]; then
        arch="arm64"
    fi

    echo "Setting up for building $arch binaries"

    dpkg --add-architecture arm64
    dpkg --add-architecture arm64

    apt-get update

    packages=(\
        #gcc-aarch64-linux-gnu \
        #g++-aarch64-linux-gnu \
        file \
        dpkg-dev \
        qemu \
        binfmt-support \
        qemu-user-static \
        pkg-config \
        ninja-build \
        doxygen \
        clang \
        python3 \
        gcc \
        g++ \
        git \
        git-lfs \
        nasm \
        cmake \
        powershell \
        libgl1-mesa-dev:$arch \
        libsoundio-dev:$arch \
        libjpeg-dev:$arch \
        libvulkan-dev:$arch \
        libx11-dev:$arch \
        libxcursor-dev:$arch \
        libxinerama-dev:$arch \
        libxrandr-dev:$arch \
        libusb-1.0-0-dev:$arch \
        libssl-dev:$arch \
        libudev-dev:$arch \
        mesa-common-dev:$arch \
        uuid-dev:$arch )

    if [ "$arch" = "amd64" ]; then
        packages+=(libopencv-dev:$arch)
    fi

    apt-get install -y --no-install-recommends ${packages[@]}
    ```

    运行setup-ubuntu.sh
    
    `sudo bash ./setup-ubuntu.sh`
    
    在编译后可能会出现powershell找不到包的情况,无法安装，但是不影响使用，可忽略，其他如果完成可进行下一步。
    
##  三 编译SDK
    
   编译过程中需要在线下载额外的依赖包，下载的地址在~/Azure-Kinect-Sensor-SDK/.gitmodules中，libyuv包需要翻墙才能下载，在这里需要将其地址改为Github上的源。在~/Azure-Kinect-Sensor-SDK/路径下按下Ctrl+h按键，显示隐藏文件，此时可以发现.gitmodules文件，找到libyuv对应的url,将其替换为下面地址即可。
   
    url = https://github.com/lemenkov/libyuv.git
    
   编译
   
    ```
    mkdir build
    cd build
    cmake .. -GNinja 
    ninja
    # 安装到系统路径中，便于后续 SDK 开发
    sudo ninja install
    ```
    
## 四 解决驱动

   1.深度引擎文件
   从该链接中https://packages.microsoft.com/ubuntu/18.04/multiarch/prod/pool/main/libk/ 下载libk4a1.4。从中解压出深度引擎文件ibdepthengine.so.2.0，复制到/usr/lib/aarch64-linux-gnu
   
   `sudo cp libdepthengine.so.2.0 /usr/lib/aarch64-linux-gnu`
   
   2.解决sudo 权限问题
   
   进入源码根目录中 scripts 文件夹,复制99-k4a.rules文件至 /etc/udev/rules.d/ 
   
   运行kaviewer时，不用添加sudo
   
   ```
   cd Azure-Kinect-Sensor-SDK/scripts 
   sudo cp 99-k4a.rules /etc/udev/rules.d/
   ```
   
 ## 五 测试
 
 
    ./build/bin/k4aviewer
   
   
    
    
