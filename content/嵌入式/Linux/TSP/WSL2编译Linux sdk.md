```json
{
    "date":"2024.01.31 17:15",
    "tags": ["BLOG","TSP","Linux","SDK","buildrootd"],
    "title": "基于WSL2搭建Linux的SDK环境并编译测试",
    "description": "基于WSL2搭建Linux的SDK环境并编译测试buildroot的全编译和独立编译",
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

### 编译buildroot

1. 选择buildrot作为操作系统

   ```shell
   export RK_ROOTFS_SYSTEM=buildroot
   ```

2. 运行全量编译命令

   ```shell
   ./build.sh all         # 只编译模块代码（u-Boot，kernel，Rootfs，Recovery）
                                   # 需要再执⾏./mkfirmware.sh 进⾏固件打包
   ```

   第一次编译需要选择电源(可这样及下下下上下上下)

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

3. 编译成功结果大约1.75小时(105min)使用5600u 16g

   ![image-20240131004637816](https://s2.loli.net/2024/01/31/CLAPsIbtxYa5gJn.png)

4. 固件打包

   ```shell
   ./mkfirmware.sh
   ```

   成功后输出路径为rockdev

   ![image-20240131004911396](https://s2.loli.net/2024/01/31/2nTfzFmuae9XMrK.png)

5. 查看输出并下载

   ![image-20240131005055875](https://s2.loli.net/2024/01/31/ku3BIO2GWtxgC47.png)

   将文件从wsl中复制到任意位置并使用开发工具进行烧录

   ![image-20240131010334771](https://s2.loli.net/2024/01/31/HuQSpJTmC3XPqoU.png)

## 独立编译

### U-Boot编译

```sh
# U-Boot编译命令
./build.sh uboot
# 查看U-Boot详细编译命令
./build.sh -h uboot
```

成功结果

![image-20240131010522653](https://s2.loli.net/2024/01/31/sfnpi3oh6FBJQTx.png)

### kernel编译

```sh
# Kernel编译命令
./build.sh kernel
# 查看Kernel详细编译命令
./build.sh -h kernel
```

成功结果

![image-20240131010642188](https://s2.loli.net/2024/01/31/bdE3W25VUnuzvN7.png)

### Recovery编译

```sh
# Recovery编译命令
./build.sh recovery
#查看Recovery详细编译命令
./build.sh -h recovery
```

成功结果

![image-20240131010803517](https://s2.loli.net/2024/01/31/Ea2xTCVZ3w1Gv5P.png)

### buildroot编译

#### Rootfs编译

```shell
./build.sh rootfs
```

成功结果![image-20240131170838701](https://s2.loli.net/2024/01/31/urdoS2ZPqDG1tmi.png)

### 模块编译

⽐如 qplayer 模块，常⽤相关编译命令如下：

- 编译 qplayer

```shell
make qplayer
```

- 重编 qplayer

```shell
make qplayer-rebuild
```

- 删除 qplayer

```shell
make qplayer-dirclean
# or
rm -rf /buildroot/output/rockchip_rk3566/build/qlayer-1.0
```

## Buildroot编译测试结束

查看sdk占用如下

![image-20240131171219192](https://s2.loli.net/2024/01/31/VQDvJcOqehsrUFC.png)

