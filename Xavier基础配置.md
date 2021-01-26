# Xavier基础配置

## 把软件源切换为国内的源

因为Xavier的架构是Arm，所以不能用普通的软件源去换(tx2也是如此)。下面给出换源的一些步骤。参考链接(https://blog.csdn.net/X_kh_2001/article/details/89198134)

1. 备份之前的sources.list

   `sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup`

2. 修改sources.list

   `sudo gedit /etc/apt/sources.list`

   Xavier清华的(此版本为ubunut18.04,如果是其他版本则需要更换):

   `deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-updates main restricted universe multiverse`
   `deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-updates main restricted universe multiverse`
   `deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-security main restricted universe multiverse`
   `deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-security main restricted universe multiverse`
   `deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-backports main restricted universe multiverse`
   `deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic-backports main restricted universe multiverse`
   `deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic main universe restricted`
   `deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ bionic main universe restricted`

   Xavier华科版本(18.04):

   `deb http://mirrors.ustc.edu.cn/ubuntu-ports/ xenial-updates main restricted universe multiverse` 
   `deb-src http://mirrors.ustc.edu.cn/ubuntu-ports/ xenial-updates main restricted universe multiverse` 
   `deb http://mirrors.ustc.edu.cn/ubuntu-ports/ xenial-security main restricted universe multiverse` 
   `deb-src http://mirrors.ustc.edu.cn/ubuntu-ports/ xenial-security main restricted universe multiverse` 
   `deb http://mirrors.ustc.edu.cn/ubuntu-ports/ xenial-backports main restricted universe multiverse` 
   `deb-src http://mirrors.ustc.edu.cn/ubuntu-ports/ xenial-backports main restricted universe multiverse` 
   `deb http://mirrors.ustc.edu.cn/ubuntu-ports/ xenial main universe restricted` 
   `deb-src http://mirrors.ustc.edu.cn/ubuntu-ports/ xenial main universe restricted`

   

3. 更新

   `sudo apt-get update && sudo apt-get upgrade`

## 其他的一些软件安装和第三方代码库的安装

可参考我的另一个仓库(https://github.com/wulalala1999/configure-in-ubuntu)。这里就不再赘述。

