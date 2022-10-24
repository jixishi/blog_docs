```json
{
    "date":"2022.10.24 14:53",
    "tags": ["BLOG","Clion","STM32","Openocd","DAP"],
    "author": "JiXieShi",
    "musicId":"5381722575"
}
```

## 软件准备

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

6. STD库模板

   - [蓝奏云](https://jxsjxs.lanzoui.com/i2QgR0eeyvsh)
   - [腾讯微云](https://share.weiyun.com/PtNvmJe1)

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

1. 安装中文化插件Chinese

2. 设置openocd和stm32cubemx的文件路径

   ![image-20221023101152803](https://i.imgur.com/V56hrwl.png)


## 创建项目

1. 使用STM32CubeMx创建项目

   ![image-20221023101337976](https://i.imgur.com/bgbxvjh.png)

2. CubeMX选择芯片

   ![image-20221023102558591](https://i.imgur.com/RA9alXp.png)

4. 芯片配置生成工具链为SW4STM32

   ![image-20221023101806756](https://i.imgur.com/P4jjrQv.png)

5. 点击GENERATE CODE开始生成代码

6. 设置OpenOcd调试文件

   ![image-20221023102943791](https://i.imgur.com/pbPYgCc.png)

7. 删除生成的两个个文件夹 Core Drivers

   ![image-20221023102957792](https://i.imgur.com/adjbrH7.png)

7. 解压STD标准库模板到目录下，并新建USER文件夹

   ![image-20221023103144349](https://i.imgur.com/oaIdBAE.png)

   ![image-20221023103703451](https://i.imgur.com/HC2z5UK.png)

9. 修改Cmake文件配置

   1. 修改引入

      原文件
      
      ```cmake
      include_directories(Core/Inc Drivers/STM32F1xx_HAL_Driver/Inc Drivers/STM32F1xx_HAL_Driver/Inc/Legacy Drivers/CMSIS/Device/ST/STM32F1xx/Include Drivers/CMSIS/Include)
      
      add_definitions(-DUSE_HAL_DRIVER -DSTM32F103xE)
      
      file(GLOB_RECURSE SOURCES "startup/*.*" "Drivers/*.*" "Core/*.*")
      ```
      
      修改后
      
      ```cmake
      include_directories(CMSIS/Inc FWlib/Inc USER/Inc)
      
      add_definitions(-DUSE_STDPERIPH_DRIVER -DSTM32F10X_HD)# STM32F10X_HD根据芯片设置
      
      file(GLOB_RECURSE SOURCES "startup/*.*" "FWlib/*.*" "CMSIS/*.*" "USER/*.*")
      ```
   
   2. 添加printf浮点支持
   
      ```cmake
      add_link_options(-specs=nano.specs -specs=nosys.specs -u _printf_float)
      ```

## 编译下载

![image-20221023104252578](https://i.imgur.com/45fvpND.png)

![image-20221023104343505](https://i.imgur.com/XEvT87P.png)

### 实用技巧

1. 串口重定向——基于魔女代码

   ```c
   #ifdef __GNUC__
   /* With GCC/RAISONANCE, small printf (option LD Linker-Libraries-Small printf
      set to Yes) calls __io_putchar() */
   #define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
   #else
   #define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
   #endif /* __GNUC__ */
   /**
     * @brief  将C语言库中的printf函数重新定位到USART上。.
     * @param  None
     * @retval None
     */
   PUTCHAR_PROTOTYPE
   {
   #if 1                                        // 方式1-使用常用的poll方式发送数据，比较容易理解，但等待耗时大
       while ((USART1->SR & 0X40) == 0);        // 等待上一次串口数据发送完成
       USART1->DR = (u8) ch;                    // 写DR,串口1将发送数据
       return ch;
   #else                                        // 方式2-使用queue+中断方式发送数据; 无需像方式1那样等待耗时，但要借助已写好的函数、环形缓冲
       uint8_t c[1] = {(uint8_t)ch};
       if (USARTx_DEBUG == USART1)    vUSART1_SendData(c, 1);
       if (USARTx_DEBUG == USART2)    vUSART2_SendData(c, 1);
       if (USARTx_DEBUG == USART3)    vUSART3_SendData(c, 1);
       if (USARTx_DEBUG == UART4)     vUART4_SendData(c, 1);
       if (USARTx_DEBUG == UART5)     vUART5_SendData(c, 1);
       return ch;
   #endif
   }
   int _write(int file, char *ptr, int len) {
       int DataIdx;
       for (DataIdx = 0; DataIdx < len; DataIdx++) { __io_putchar(*ptr++); }
       return len;
   }
   ```

   2. nop指令替换

       `__nop();` => `__asm("nop");`

