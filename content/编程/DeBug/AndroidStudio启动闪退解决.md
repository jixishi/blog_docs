```json
{
    "date":"2023.01.06 12:38",
    "tags": ["BLOG","Clion","STM32","Openocd","DAP"],
    "author": "JiXieShi",
    "musicId":"5381722575"
}
```

## 缘由

距离上次更新AS大概过了一年多心血来潮想更新试试

然后更新完就无法启动，studio64.exe也不能启动

于是使用studio.bat启动发现是idea.system.path这个配置找不到

突然想起第一次安装也是这个问题不过那会有弹窗

## 解决

打开AndroidStudio安装目录下的bin文件夹

![image-20230106123854838](https://i.imgur.com/PI9RGE5.png)

其中有idea.properties文件打开他修改成如下

```properties
idea.config.path=${user.home}/.AndroidStudio/config

idea.system.path=${user.home}/.AndroidStudio/system

idea.plugins.path=${idea.config.path}/plugins
```

也就是去掉这三行前的#字符

## 关于汉化

[中文包下载](https://plugins.jetbrains.com/plugin/13710-chinese-simplified-language-pack----/versions)

版本确认如这里就是需要213版本

![image-20230106124247712](https://i.imgur.com/td8IF8P.png)

下载后在file→setting→plugins页面中右上角的设置按钮→install plugins from disk，找到刚才下载插件的目录，选择此插件，然后会提醒你restart，点击重启后再进入就是已经汉化完成的界面了。

![image-20230106124327677](https://i.imgur.com/BQd7sgU.png)