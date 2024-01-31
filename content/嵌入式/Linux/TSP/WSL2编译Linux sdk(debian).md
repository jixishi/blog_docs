```json
{
    "date":"2024.01.31 22:11",
    "tags": ["BLOG","TSP","Linux","SDK","debian","Ubuntu"],
    "title": "基于WSL2搭建Linux的SDK环境并编译测试(Debian和Ubuntu)",
    "description": "基于WSL2搭建Linux的SDK环境并编译测试debian的全编译和Ubuntu的文件系统制作",
    "author": "JiXieShi",
    "musicId":"5381722575"
}
```



# 基于WSL2 搭建Linux的SDK环境并编译测试

## 目录

[TOC]



## 环境准备

### 工具下载与安装

- wsl 2 安装的安装参考这个文章即可

  [Windows上快速安装WSL2教程](https://developer.aliyun.com/article/886462)

- 体验增强工具 WSL Manager

  ![image-20240130214232792](https://s2.loli.net/2024/01/31/mkBvVc9jOf8dGpP.png)获取地址如下:

  1. [GitHub](https://github.com/bostrot/wsl2-distro-manager)
  2. [微软商店](https://www.microsoft.com/store/productId/9NWS9K95NMJB?ocid=pdpshare)

- Window terminal(可选，Win11自带可以不用看)

  获取地址 [微软商店](https://www.microsoft.com/store/productId/9N0DX20HK701?ocid=pdpshare)

- 下载好官方提供的sdk文件

### 获取Ubuntu 18编译环境

1. 打开WSL Manager如下(这里已经配置好了)![image-20240130214600214](https://s2.loli.net/2024/01/31/uIqYnFSa32LCU4B.png)

2. 点击左边加号(+)图标进入此界面![image-20240130214816069](https://s2.loli.net/2024/01/31/1AW4PmoiVEjqb5y.png)

3. 填入名称(建议英文) 、点击发行版等待在线镜像列表加载如下图(这里选择Ubuntu 18.04) 、保存位置默认在C:\WSL2-Distros下可选其他磁盘(优先固态)、用户这里不填![image-20240130215122588](https://s2.loli.net/2024/01/31/S9xbeomkDrP6VQy.png)

4. 点击创建即可得到编译系统环境，接下来点击房子图标回到主界面此时会有刚刚创建好的实例，点击右边箭头打开功能选板![image-20240130215521304](https://s2.loli.net/2024/01/31/LpWfaugXP9FSY4k.png)

5. 点击文件夹打开虚拟机内部文件列表如图(也可以在资源管理器的左侧Linux下打开)![image-20240130220051134](https://s2.loli.net/2024/01/31/2xdD147vuNt9LYh.png)

6. 点击root文件夹将sdk压缩包复制粘贴进去![image-20240130220251351](https://s2.loli.net/2024/01/31/XvEezS8idMI4PqF.png)

7. 点击功能选板中的终端按钮进入类似界面![image-20240130220731665](https://s2.loli.net/2024/01/31/1wMgrcymUxD4jiA.png)

8. 输入指令 `ls -lh` 查看sdk压缩包是否完整显示在终端中(体积为14g)![image-20240130221143144](https://s2.loli.net/2024/01/31/bNBitcq1uQ4TzUV.png)

9. 输入指令 `tar zxvf tspi_linux_sdk_20230916.tar.gz && ls` 等待片刻如下为解压完成(Release目录出现)![image-20240130221425207](https://s2.loli.net/2024/01/31/mYSBvgoZKPjsh9U.png)

10. 可选操作 对sdk解压出来的路径改名并删除sdk压缩包，其相关代码如下

    ```shell
    rm -rf tspi_linux_sdk_20230916.tar.gz
    mv Release/ Linux_SDK
    ```

11. 进入更名后的sdk目录准备开始编译，其代码和结果如下

    ```shell
    cd Linux_SDK/
    ls
    ```

    ![image-20240130221925938](https://s2.loli.net/2024/01/31/u3CBOgyQMdaUSte.png)

### 编译环境初始化

1. 换源(可选)

   ```shell
   bash <(curl -sSL https://linuxmirrors.cn/main.sh)
   ```

   ![image-20240130222823053](https://s2.loli.net/2024/01/31/ZYcHoud9VznvXL7.png)

   如图选择你喜欢的源即可(这里我选择阿里云则输入1即可)输入对应序号回车确认

   等待更换完成，之后都回车即可

   ![image-20240130223017863](https://s2.loli.net/2024/01/31/DOe9PMn3ESgKacV.png)

2. 使用如下代码安装所需工具链及依赖

   ```shell
   sudo apt-get update
   sudo apt-get install -y git ssh make gcc libssl-dev liblz4-tool expect \
   g++ patchelf chrpath gawk texinfo chrpath diffstat binfmt-support \
   qemu-user-static live-build bison flex fakeroot cmake gcc-multilib \
   g++-multilib unzip device-tree-compiler ncurses-dev python-minimal
   ```

3. 等待安装完成(如果换源大概1分钟内)，成功结果如下

   ![image-20240130223411746](https://s2.loli.net/2024/01/31/fhBVFyvKICOgk2G.png)

### 泰山派SDK板级配置

根据官方文档所说开始编译前需要先设置泰山派的板级配置，配置方法有两种：

1. ```shell
   ./build.sh device/rockchip/rk356x/BoardConfig-rk3566-tspi-v10.mk
   ```

   ![image-20240130223719688](https://s2.loli.net/2024/01/31/XzW18c5Pu3gEa9F.png)

2. ```shell
   ./build.sh lunch
   ```

   ![image-20240130223750483](https://s2.loli.net/2024/01/31/VoqMcfjPU5TDyIm.png)

查看配置

```shell
 ./build.sh -h kernel
```

![image-20240130223850958](https://s2.loli.net/2024/01/31/T2ZLMIxS53YDUy7.png)

成功生效如图

### 编译debian

1. 选择buildrot作为操作系统

   ```shell
   export RK_ROOTFS_SYSTEM=debian
   ```

2. 依赖补全

   ```shell
   cd debian && dpkg -i ubuntu-build-service/packages/* 
   apt-get install -f -y && cd ..
   ```

   ![image-20240131205905524](https://s2.loli.net/2024/01/31/FQrN1pwO7PmaHMg.png)

3. 运行全量编译命令

   ```shell
   ./build.sh all         # 只编译模块代码（u-Boot，kernel，Rootfs，Recovery）
                                   # 需要再执⾏./mkfirmware.sh 进⾏固件打包
   ```

   第一次编译需要选择电源(可这样记操作 下下下上下上下)

   | 引脚           | PMUIO2 | VCCIO1 | VCCIO3 | VCCIO4 | VCCIO5 | VCCIO6 | VCCIO7 |
   | -------------- | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
   | 1.8 V(1800000) |        |        |        | √      |        | √      |        |
   | 3.3 V(3300000) | √      | √      | √      |        | √      |        | √      |

   分布操作如下

   ![image-20240131170241405](https://s2.loli.net/2024/01/31/MdlASobK4fD8mIX.png)

   ![image-20240131170301614](https://s2.loli.net/2024/01/31/a9FzAreyD2uVCQ7.png)

   ![image-20240131170308275](https://s2.loli.net/2024/01/31/tNTCHSxIh26pPr1.png)

   ![image-20240131170317464](https://s2.loli.net/2024/01/31/c9l7QeN6D5qhBvo.png)

   ![image-20240131170333017](https://s2.loli.net/2024/01/31/jdNrl3sE8H6eDum.png)

   ![image-20240131170342262](https://s2.loli.net/2024/01/31/Txk1WXfLl96pbDs.png)

   ![image-20240131170352824](https://s2.loli.net/2024/01/31/LUG498jnVbWR16Y.png)

4. 编译成功 20.54 21.33结果大约**0.65**小时(**39**min)使用5600u 16g

   ![image-20240131213402233](https://s2.loli.net/2024/01/31/PKm6ICw4hqgoucn.png)

5. 固件打包

   ```shell
   ./mkfirmware.sh
   ```

   成功后输出路径为rockdev

   ![image-20240131213723902](https://s2.loli.net/2024/01/31/1KRPBVwrfTFJGmX.png)

6. 查看输出并下载

   ![image-20240131005055875](https://s2.loli.net/2024/01/31/ku3BIO2GWtxgC47.png)

   将文件从wsl中复制到任意位置并使用开发工具进行烧录，配置文件获取[下载地址](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/all/Z9AXbkTwDoMJzhxMXDtcZbbrncb/?mount_node_token=XVCQdtbIfo9FRYxZHpdcrFYnnkf&mount_point=docx_file)后修改为对应的路径

   ![image-20240131010334771](https://s2.loli.net/2024/01/31/HuQSpJTmC3XPqoU.png)

## Ubuntu根文件系统制作

#### 修改根文件系统

1. 创建一个普通用户相关命令如下

   ```shell
   adduser username
   usermod -aG sudo username
   ```

2. 切换至普通用户并下载基础文件系统

   ```shell
   su username && cd
   wget https://cdimage.ubuntu.com/ubuntu-base/releases/18.04/release/ubuntu-base-18.04.5-base-arm64.tar.gz
   ```

3. 创建temp目录并解压基础文件系统

   ```shell
   mkdir temp
   tar -xpf ubuntu-base-18.04.5-base-arm64.tar.gz -C temp/
   ```

4. 安装qemu相关依赖并准备arm-ubuntu文件系统的网络和qemu

   ```shell
   sudo apt-get install qemu-user-static
   cp -b /etc/resolv.conf temp/etc/resolv.conf
   cp /usr/bin/qemu-aarch64-static temp/usr/bin/
   ```

5. 创建切换脚本相关命令如下

   ```shell
   cat >> ~/rootfs-mount.sh <<EOF
   #!/bin/bash
   function mnt() {
       echo "MOUNTING"
       sudo update-binfmts --enable qemu-arm
   
       sudo mount -t proc /proc ${2}/proc
       sudo mount -t sysfs /sys ${2}/sys
       sudo mount -o bind /dev ${2}/dev
   
       sudo chroot ${2}
   }
   function umnt() {
       echo "UNMOUNTING"
   
       sudo umount ${2}/proc
       sudo umount ${2}/sys
       sudo umount ${2}/dev
   
       sudo update-binfmts --disable qemu-arm
   }
   if [ "$1" == "-m" ] && [ -n "$2" ] ;
   then
       mnt $1 $2
   elif [ "$1" == "-u" ] && [ -n "$2" ];
   then
       umnt $1 $2
   else
       echo ""
       echo "Either 1'st, 2'nd or both parameters were missing"
       echo ""
       echo "1'st parameter can be one of these: -m(mount) OR -u(umount)"
       echo "2'nd parameter is the full path of rootfs directory(with trailing '/')"
       echo ""
       echo "For example: ch-mount -m /media/sdcard/"
       echo ""
       echo 1st parameter : ${1}
       echo 2nd parameter : ${2}
   fi
   EOF
   ```

6. 将执行权限赋予切换脚本，并进行文件系统切换

   ```shell
   sudo chmod +x rootfs-mount.sh
   sudo bash rootfs-mount.sh -m temp
   ```

   结果如图

   ![image-20240131202446772](https://s2.loli.net/2024/01/31/biOoUtHmsqLgW6G.png)

7. 更新源

   ```shell
   apt-get update && apt upgrade
   ```

   结果如下

   ![image-20240131202652231](https://s2.loli.net/2024/01/31/w6c3nG4JRlSKsMP.png)

8. 安装基本依赖包

   ```shell
   apt install -y vim git ssh curl ethtool rsyslog bash-completion htop net-tools wireless-tools network-manager iputils-ping language-pack-en-base ifupdown
   apt install -y inetutils-ping cutecom audacity v4l-utils cheese wpasupplicant
   ```

9. (可选)安装图像桌面

   ```shell
   apt-get install xubuntu-desktop
   ```

#### 配置网络



```shell
vim /etc/network/interfaces

source-directory /etc/network/interfaces.d
#以太网
auto eth0
iface eth0 inet dhcp
#wifi
auto wlan0
iface wlan0 inet dhcp
wpa-conf /etc/wpa_config.conf
```

```shell
vim /etc/resolv.conf

nameserver 8.8.8.8
nameserver 114.114.114.114
```

#### 添加用户并配置密码

添加用户：

```shell
useradd -s '/bin/bash' -m -G adm,sudo username
```

给用户设置密码：

```shell
passwd username
```

给root用户设置密码：

```shell
passwd root
```

自定义完根文件系统就可以退出了。

```shell
exit
sudo bash rootfs-mount.sh -u temp
```

#### 制作根文件系统

1. 查看体积

   ```shell
   sudo du -h --max-depth=1
   ```

   ![image-20240131220220024](https://s2.loli.net/2024/01/31/4sXUClb5pjr3AHm.png)

2. 制作自己的根文件系统，大小依据自己的根文件系统而定，注意依据 `temp` 文件夹的大小来修改 `count` 值，因为我们这里的大小为1015M所以count=2000*bs=1M保证大于我们的temp。

   ```shell
   mkdir  rootfs
   dd if=/dev/zero of=linuxroot.img bs=1M count=2000
   mkfs.ext4 linuxroot.img
   sudo mount linuxroot.img rootfs/
   sudo cp -rfp temp/*  rootfs/
   sudo umount rootfs/
   e2fsck -p -f linuxroot.img
   resize2fs  -M linuxroot.img
   ```

   ![image-20240131220516621](https://s2.loli.net/2024/01/31/X8JEteHbrlfokFu.png)

3. 这样 `linuxroot.img` 就是最终的根文件系统映像文件,将其移出至任意磁盘以便下载。

#### 下载

参考前文使用开发工具下载将文件从wsl中复制到任意位置并使用开发工具进行烧录，配置文件获取[下载地址](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/all/Z9AXbkTwDoMJzhxMXDtcZbbrncb/?mount_node_token=XVCQdtbIfo9FRYxZHpdcrFYnnkf&mount_point=docx_file)后修改为对应的路径

![image-20240131010334771](https://s2.loli.net/2024/01/31/HuQSpJTmC3XPqoU.png)
