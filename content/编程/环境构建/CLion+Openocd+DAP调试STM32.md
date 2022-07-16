```json
{
    "date":"2022.06.12 17:38",
    "tags": ["BLOG","Clion","STM32","Openocd","DAP"],
    "author": "JiXieShi",
    "musicId":"5381722575"
}
```

## 软件下载

1. Clion的下载

   - 便携版--免账号(版本低)

     [Clion2021.1.2]: https://www.ghxi.com/clion.html	"果壳剥壳"

   - 官网安装版--需要购买账号(最新)

     [最新]: https://www.jetbrains.com/clion/	"Jetbrains"

2. Gcc下载(Clion新版本免安装内置)
   - [TDM-Gcc](https://jmeubank.github.io/tdm-gcc/)
   - [Mingw](https://www.mingw-w64.org/downloads/)

3. ArmGcc工具链下载
   - [ArmGnuToolchain](https://developer.arm.com/downloads/-/gnu-rm)
4. OpenOCD调试器下载
   - [官网](https://openocd.org/)
5. CubeMx下载
   - [STM32CubeMX](https://www.st.com/content/st_com/en/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-configurators-and-code-generators/stm32cubemx.html#get-software) --需填写邮件接收下载地址

## 环境安装

1. 安装前面的软件并记录位置

2. 设置以下环境变量

| 变量名  | 变量值                     | 例如                                       |
| :------ | :------------------------- | :----------------------------------------- |
| Gcc_Arm | Arm工具链安装路径\bin      | D:\GNU Arm Embedded Toolchain\9 2019.9\bin |
| OpenOCD | OpenOCD安装路径            | D:\openocd\xpack-openocd-0.10.0-15         |
| GCC     | GCC安装目录\bin            | D:\TDM-GCC-64\bin                          |
| PATH    | ;%Gcc_Arm%;%OpenOCD%;%GCC% | ;%Gcc_Arm%;%OpenOCD%;%GCC%                 |

3. 重启电脑(可选操作)

## OpenOCD驱动编写

1. DAP

   ```ini
   source [find interface/cmsis-dap.cfg] #使用dap驱动
   transport select swd # 使用swd调试
   source [find target/stm32f1x.cfg] # 其他板卡切换这个即可
   reset_config none
   ```

2. Jlink

   ```ini
   source [find interface/jlink.cfg] #使用jlink驱动,需要zadig的驱动文件
   transport select swd # 使用swd调试
   source [find target/stm32f1x.cfg] # 其他板卡切换这个即可
   adapter speed 4000
   ```

3. stlink

   ```ini
   source [find interface/stlink.cfg] #使用stlinkv1驱动,注意v2要用stlink-v2.cfg或者stlink-v2-1.cfg
   transport select hla_swd
   source [find target/stm32f1x.cfg]
   reset_config none
   ```

## Clion设置

1. 安装中文插件Chinese

2. 设置openocd和stm32cubemx的文件路径

   ![设置文件路径](./assets/images/Clion/%E8%AE%BE%E7%BD%AE%E6%96%87%E4%BB%B6%E8%B7%AF%E5%BE%84.png)

