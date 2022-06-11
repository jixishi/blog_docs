```json
{
    "date":"2022.03.16 17:38",
    "tags": ["Orange Pi","Linux","ESP-IDF","ESP","VSC"],
    "author": "JiXieShi", 
    "musicId":"5381722575"
}
```

## 联网

```shell
# 切换用户为root
su root
# 开启WiFi
nmcli r wifi on
# 扫描附近WiFi信息
nmcli dev wifi
# 连接WiFi SSID为WiFi名 PASSWORD为WiFi密码
nmcli dev wifi connect "SSID" password "PASSWORD" ifname wlan0
```

## 更新软件包

```shell
sudo apt-get update
```

## 安装code-server

```shell
# 安装前置工具
sudo apt-get install curl git wget vim
# 下载codeserver安装包
wget https://github.com/coder/code-server/releases/download/v4.4.0/code-server_4.4.0_arm64.deb # GitHub
wget https://ghproxy.com/?q=https://github.com/coder/code-server/releases/download/v4.4.0/code-server_4.4.0_arm64.deb # 国内加速
# 开始安装
sudo dpkg -i code-server_4.4.0_arm64.deb
# 运行一次以输出配置文件
code-server
# 修改配置文件
vim ~/.config/code-server/config.yaml
# 设置自启及启动
sudo systemctl enable --now code-server@$USER
```

## vsc扩展插件

1. indent-rainbow					空格标识
2. Bracket Pair Colorizer            彩色代码块 
3. Chinese Language Pack             中文语言包
4. Code Runner                       一键编译运行
5. Prettier - Code formatter         代码分色
6. vscode-icons                      文件图标
7. Path Intellisense                 路径速补
8. C/C++ Themes                      程序提示
9. CMake
10. CMake Tools

## 配置ESP-IDF开发平台

1. 前置准备

   ```shell
   # 安装依赖
   sudo apt-get install git wget flex bison gperf python3 python3-venv python3-setuptools cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0
   # 检测python3是否存在
   python3 --version
   ```

2. 获取IDF

   ```shell
   mkdir -p ~/esp
   cd ~/esp
   git clone --recursive https://github.com/espressif/esp-idf.git
   ```

3. 安装idf构建工具

   ```shell
   cd ~/esp/esp-idf
   export IDF_GITHUB_ASSETS="dl.espressif.com/github_assets"# 可选官网下载加速,默认github
   ./install.sh all # 这里是全量安装支持ESP32绝大部分板子,可选有(esp32,esp32s2,esp32s3,esp32c2,esp32c3)
   ```

4. 添加环境变量

   ```shell
   . $HOME/esp/esp-idf/export.sh
   ```

5. 开始使用

   ```shell
   cd ~/esp
   cp -r $IDF_PATH/examples/get-started/hello_world .
   cd ~/esp/hello_world
   # 设置编译目标芯片
   idf.py set-target esp32
   # 交互配置界面
   idf.py menuconfig
   ```

6. 编译与烧录，调试

   ```shell
   # 编译
   idf.py build
   #烧录 调试(使用此命令可免去编译)
   export ESPPORT="/dev/tty/ttyACM0" # 设置串口终端(Linux:/dev/tty/ttyACM*)
   idf.py flash monitor
   ```

## 参考文档

- [ESP-IDF](https://docs.espressif.com/projects/esp-idf/zh_CN/latest/esp32/get-started/linux-macos-setup.html) ————ESP官方安装指南
- [Code-server](https://github.com/coder/code-server) ————VSC在线版官方仓库地址