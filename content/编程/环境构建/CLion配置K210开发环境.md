```json
{
    "date":"2022.08.7 17:08",
    "tags": ["BLOG","Clion","K210","Openocd","K-flash"],
    "author": "JiXieShi",
    "musicId":"5381722575"
}
```

## 软件获取

## CLion配置

1. 配置K-Flash外部工具

   ![image-20220814112724780](https://i.imgur.com/jf2Xm35.png)

   - COM37 替换为自己的串口号
   - 程序kflash.exe 替换为自己的完整路径
   - 工作目录为固件编译完输出的那层路径

2. 配置编译工具链

   ![image-20220814113037174](https://i.imgur.com/4lOYmrl.png)

   如图新建工具链

   各个程序换为下载的路径
