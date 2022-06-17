```json
{
    "date":"2022.06.13 17:06",
    "tags": ["BLOG","F103C8T6","C8T6","HAL","数码管"],
    "description": "基于HAL库的魔女F1C8T6开发板所写的四种8共阴数码管驱动",
    "author": "JiXieShi",
    "image": "./assets/images/HAL/%E9%AD%94%E5%A5%B3F103C8T6.jpg",
    "musicId":"5381722575"
}
```

## 板卡展示

![魔女F1C8T6](./assets/images/HAL/%E9%AD%94%E5%A5%B3F103C8T6.jpg)

## 驱动源码

### H文件

```c
#ifndef HAL_SMG_H
#define HAL_SMG_H

#include"main.h"

//驱动选择

//#define HC595 //取消注释启用595驱动
//#define IO 1 //取消注释启用IO驱动
//#define IOCS 1 //取消注释启用IO位选
//#define HC138 1 //取消注释启用138驱动
//#define Y_OR_Y 1 //取消注释启用共阳


//HC138重定义

#define HC138_A0_H   HAL_GPIO_WritePin(HC138_A0_GPIO_Port,HC138_A0_Pin,GPIO_PIN_SET)
#define HC138_A0_L   HAL_GPIO_WritePin(HC138_A0_GPIO_Port,HC138_A0_Pin,GPIO_PIN_RESET)
#define HC138_A1_H   HAL_GPIO_WritePin(HC138_A1_GPIO_Port,HC138_A1_Pin,GPIO_PIN_SET)
#define HC138_A1_L   HAL_GPIO_WritePin(HC138_A1_GPIO_Port,HC138_A1_Pin,GPIO_PIN_RESET)
#define HC138_A2_H   HAL_GPIO_WritePin(HC138_A2_GPIO_Port,HC138_A2_Pin,GPIO_PIN_SET)
#define HC138_A2_L   HAL_GPIO_WritePin(HC138_A2_GPIO_Port,HC138_A2_Pin,GPIO_PIN_RESET)

//HC595重定义

#define HC595_DATA_H   HAL_GPIO_WritePin(HC595_DATA_GPIO_Port, HC595_DATA_Pin, GPIO_PIN_SET)
#define HC595_DATA_L   HAL_GPIO_WritePin(HC595_DATA_GPIO_Port, HC595_DATA_Pin, GPIO_PIN_RESET)
#define HC595_LCLK_H   HAL_GPIO_WritePin(HC595_LCLK_GPIO_Port, HC595_LCLK_Pin, GPIO_PIN_SET)
#define HC595_LCLK_L   HAL_GPIO_WritePin(HC595_LCLK_GPIO_Port, HC595_LCLK_Pin, GPIO_PIN_RESET)
#define HC595_SCLK_H   HAL_GPIO_WritePin(HC595_SCLK_GPIO_Port, HC595_SCLK_Pin, GPIO_PIN_SET)
#define HC595_SCLK_L   HAL_GPIO_WritePin(HC595_SCLK_GPIO_Port, HC595_SCLK_Pin, GPIO_PIN_RESET)

//IO驱动重定义
#if IO
#define A_H   HAL_GPIO_WritePin(A_GPIO_Port,A_Pin,GPIO_PIN_SET)
#define A_L   HAL_GPIO_WritePin(A_GPIO_Port,A_Pin,GPIO_PIN_RESET)
#define B_H   HAL_GPIO_WritePin(B_GPIO_Port,B_Pin,GPIO_PIN_SET)
#define B_L   HAL_GPIO_WritePin(B_GPIO_Port,B_Pin,GPIO_PIN_RESET)
#define C_H   HAL_GPIO_WritePin(C_GPIO_Port,C_Pin,GPIO_PIN_SET)
#define C_L   HAL_GPIO_WritePin(C_GPIO_Port,C_Pin,GPIO_PIN_RESET)
#define D_H   HAL_GPIO_WritePin(D_GPIO_Port,D_Pin,GPIO_PIN_SET)
#define D_L   HAL_GPIO_WritePin(D_GPIO_Port,D_Pin,GPIO_PIN_RESET)
#define E_H   HAL_GPIO_WritePin(E_GPIO_Port,E_Pin,GPIO_PIN_SET)
#define E_L   HAL_GPIO_WritePin(E_GPIO_Port,E_Pin,GPIO_PIN_RESET)
#define F_H   HAL_GPIO_WritePin(F_GPIO_Port,F_Pin,GPIO_PIN_SET)
#define F_L   HAL_GPIO_WritePin(F_GPIO_Port,F_Pin,GPIO_PIN_RESET)
#define G_H   HAL_GPIO_WritePin(G_GPIO_Port,G_Pin,GPIO_PIN_SET)
#define G_L   HAL_GPIO_WritePin(G_GPIO_Port,G_Pin,GPIO_PIN_RESET)
#define DP_H   HAL_GPIO_WritePin(DP_GPIO_Port,DP_Pin,GPIO_PIN_SET)
#define DP_L   HAL_GPIO_WritePin(DP_GPIO_Port,DP_Pin,GPIO_PIN_RESET)
#endif
//IO位选重定义
#if IOCS
#define CS0_H   HAL_GPIO_WritePin(CS0_GPIO_Port,CS0_Pin,GPIO_PIN_SET)
#define CS0_L   HAL_GPIO_WritePin(CS0_GPIO_Port,CS0_Pin,GPIO_PIN_RESET)
#define CS1_H   HAL_GPIO_WritePin(CS1_GPIO_Port,CS1_Pin,GPIO_PIN_SET)
#define CS1_L   HAL_GPIO_WritePin(CS1_GPIO_Port,CS1_Pin,GPIO_PIN_RESET)
#define CS2_H   HAL_GPIO_WritePin(CS2_GPIO_Port,CS2_Pin,GPIO_PIN_SET)
#define CS2_L   HAL_GPIO_WritePin(CS2_GPIO_Port,CS2_Pin,GPIO_PIN_RESET)
#define CS3_H   HAL_GPIO_WritePin(CS3_GPIO_Port,CS3_Pin,GPIO_PIN_SET)
#define CS3_L   HAL_GPIO_WritePin(CS3_GPIO_Port,CS3_Pin,GPIO_PIN_RESET)
#define CS4_H   HAL_GPIO_WritePin(CS4_GPIO_Port,CS4_Pin,GPIO_PIN_SET)
#define CS4_L   HAL_GPIO_WritePin(CS4_GPIO_Port,CS4_Pin,GPIO_PIN_RESET)
#define CS5_H   HAL_GPIO_WritePin(CS5_GPIO_Port,CS5_Pin,GPIO_PIN_SET)
#define CS5_L   HAL_GPIO_WritePin(CS5_GPIO_Port,CS5_Pin,GPIO_PIN_RESET)
#define CS6_H   HAL_GPIO_WritePin(CS6_GPIO_Port,CS6_Pin,GPIO_PIN_SET)
#define CS6_L   HAL_GPIO_WritePin(CS6_GPIO_Port,CS6_Pin,GPIO_PIN_RESET)
#define CS7_H   HAL_GPIO_WritePin(CS7_GPIO_Port,CS7_Pin,GPIO_PIN_SET)
#define CS7_L   HAL_GPIO_WritePin(CS7_GPIO_Port,CS7_Pin,GPIO_PIN_RESET)
#endif
//数码管驱动函数定义
#if IO && HC138
void LED_IO_138_W(uint8_t duan, uint8_t wei);
#endif
#if IO && IOCS
void LED_IO_CS_W(uint8_t duan, uint8_t wei);
#endif
#if HC595 && HC138
void LED_HC595_138_W(uint8_t duan, uint8_t wei);
#endif
#if HC595
void LED_HC595_595_W(uint8_t duan, uint8_t wei);
#endif
#endif //HAL_SMG_H

```

### C文件

```c
#include "smg.h"

//共阴数字数组
const uint8_t smg_num[]={0x3f,0x06,0x5b,0x4f,0x66,0x6d,0x7d,0x07,0x7f,0x6f,0x77,0x7c,0x39,0x5e,0x79,0x71};
//位选数组
const uint8_t smg_weis[]={0xfe,0xfd,0xfb,0xf7,0xef,0xdf,0xbf,0x7f};

#if HC138
//74HC138驱动
//数码管位选
//num:要显示的数码管编号 0-7(共8个数码管)
void HC138_Wei(uint8_t num)
{
    if(num&0x01)HC138_A0_H;
    else HC138_A0_L;

    if(num&0x02)HC138_A1_H;
    else HC138_A1_L;

    if(num&0x04)HC138_A2_H;
    else HC138_A2_L;
}
#endif
//获取码值
uint8_t Get_Num(uint8_t duan)
{
#if Y_OR_Y
    uint8_t smg_duan = ~smg_num[duan];
#else
    uint8_t smg_duan = smg_num[duan];
#endif
    return smg_duan;
}
#if IO
/*IO驱动，数码管显示*/
void IO_Write_Data(uint8_t smg_duan){
    (smg_duan<<0)&0x80 ? DP_H : DP_L;
    (smg_duan<<1)&0x80 ? G_H : G_L;
    (smg_duan<<2)&0x80 ? F_H : F_L;
    (smg_duan<<3)&0x80 ? E_H : E_L;
    (smg_duan<<4)&0x80 ? D_H : D_L;
    (smg_duan<<5)&0x80 ? C_H : C_L;
    (smg_duan<<6)&0x80 ? B_H : B_L;
    (smg_duan<<7)&0x80 ? A_H : A_L;
}
#endif
#if IOCS
/*IO驱动，数码管显示*/
void IO_Wei(uint8_t smg_duan){
    (smg_duan<<0)&0x80 ? CS0_H : CS0_L;
    (smg_duan<<1)&0x80 ? CS1_H : CS1_L;
    (smg_duan<<2)&0x80 ? CS2_H : CS2_L;
    (smg_duan<<3)&0x80 ? CS3_H : CS3_L;
    (smg_duan<<4)&0x80 ? CS4_H : CS4_L;
    (smg_duan<<5)&0x80 ? CS5_H : CS5_L;
    (smg_duan<<6)&0x80 ? CS6_H : CS6_L;
    (smg_duan<<7)&0x80 ? CS7_H : CS7_L;
}
#endif
/*74HC595驱动,数码管刷新显示*/
#if HC595
void HC595_Refresh(void)
{
    HC595_LCLK_H;
    Delay_Us(5);
    HC595_LCLK_L;
}
void HC595_Wei(uint8_t num)
{
    uint8_t i;
    uint8_t smg_wei = smg_weis[num];
    for( i=0;i<8;i++)
    {
        if(smg_wei&0x80)HC595_DATA_H;
        else HC595_DATA_L;
        HC595_SCLK_L;
        Delay_Us(5);
        HC595_SCLK_H;
        smg_wei<<=1;
    }
    HC595_Refresh();
}
//74HC595驱动
void HC595_Write_Data(uint8_t duan)
{
    uint8_t i;
    uint8_t smg_duan = Get_Num(duan)
    for( i=0;i<8;i++)
    {
        if(smg_duan&0x80)HC595_DATA_H;
        else HC595_DATA_L;
        HC595_SCLK_L;
        Delay_Us(5);
        HC595_SCLK_H;
        smg_duan<<=1;
    }
    HC595_Refresh();
}
#endif
#if HC595 && HC138
/**
* @brief HC595+HC138显示
* @param duan: 字符显示(0-F)
* @param wei: 位选(0-7)
* @retval None
*/
void LED_HC595_138_W(uint8_t duan, uint8_t wei){
    HC138_Wei(wei);
    HC595_Write_Data(duan);
}
#endif
#if IO && HC138
/**
* @brief IO+HC138显示
* @param duan: 字符显示(0-F)
* @param wei: 位选(0-7)
* @retval None
*/
void LED_IO_138_W(uint8_t duan, uint8_t wei){
    HC138_Wei(wei);
    IO_Write_Data(Get_Num(duan));
}
#endif
#if IO && IOCS
/**
* @brief IO+IOCS显示
* @param duan: 字符显示(0-F)
* @param wei: 位选(0-7)
* @retval None
*/
void LED_IO_CS_W(uint8_t duan, uint8_t wei){
    IO_Wei(~smg_weis[wei]);
    IO_Write_Data(Get_Num(duan));
}
#endif
#if HC595
/**
* @brief 双HC595显示
* @param duan: 字符显示(0-F)
* @param wei: 位选(0-7)
* @retval None
*/
void LED_HC595_595_W(uint8_t duan, uint8_t wei){
    HC595_Wei(wei);
    HC595_Write_Data(duan);
}
#endif
```




## 使用实例

1. HC595+HC138

   ```c
   for(int i=0;i<8;i++){
   	LED_HC595_138_W(i,i);
   	osDelay(2);
   }
   ```

2. 双HC595(先选后显)

   ```c
   for(int i=0;i<8;i++){
   	LED_HC595_595_W(i,i);
   	osDelay(2);
   }
   ```
3. IO显示HC138位选

   ```c
   for(int i=0;i<8;i++){
   	LED_IO_138_W(i,i);
   	osDelay(2);
   }
   ```
4. IO显示IO位选

   ```c
   for(int i=0;i<8;i++){
   	LED_IO_CS_W(i,i);
   	osDelay(2);
   }
   ```

## 注意事项

在魔女C8T6开发板中因板载dap原因有如下引脚不能作为HC595DATA

- PB4
- PB3
- PA15
- PA14
- PA13

没错就是JTAG和SWD调试会用的引脚(ps:因为这几个IO与c6t6直连)

 
