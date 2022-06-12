## 驱动源码

>### H文件
>
>```c
>#ifndef HAL_SMG_H
>#define HAL_SMG_H
>
>#include"main.h"
>
>#define HC138_A0_H   HAL_GPIO_WritePin(HC138_A0_GPIO_Port,HC138_A0_Pin,GPIO_PIN_SET)
>#define HC138_A0_L   HAL_GPIO_WritePin(HC138_A0_GPIO_Port,HC138_A0_Pin,GPIO_PIN_RESET)
>#define HC138_A1_H   HAL_GPIO_WritePin(HC138_A1_GPIO_Port,HC138_A1_Pin,GPIO_PIN_SET)
>#define HC138_A1_L   HAL_GPIO_WritePin(HC138_A1_GPIO_Port,HC138_A1_Pin,GPIO_PIN_RESET)
>#define HC138_A2_H   HAL_GPIO_WritePin(HC138_A2_GPIO_Port,HC138_A2_Pin,GPIO_PIN_SET)
>#define HC138_A2_L   HAL_GPIO_WritePin(HC138_A2_GPIO_Port,HC138_A2_Pin,GPIO_PIN_RESET)
>
>#define HC595_DATA_H   HAL_GPIO_WritePin(HC595_DATA_GPIO_Port, HC595_DATA_Pin, GPIO_PIN_SET)
>#define HC595_DATA_L   HAL_GPIO_WritePin(HC595_DATA_GPIO_Port, HC595_DATA_Pin, GPIO_PIN_RESET)
>#define HC595_LCLK_H   HAL_GPIO_WritePin(HC595_LCLK_GPIO_Port, HC595_LCLK_Pin, GPIO_PIN_SET)
>#define HC595_LCLK_L   HAL_GPIO_WritePin(HC595_LCLK_GPIO_Port, HC595_LCLK_Pin, GPIO_PIN_RESET)
>#define HC595_SCLK_H   HAL_GPIO_WritePin(HC595_SCLK_GPIO_Port, HC595_SCLK_Pin, GPIO_PIN_SET)
>#define HC595_SCLK_L   HAL_GPIO_WritePin(HC595_SCLK_GPIO_Port, HC595_SCLK_Pin, GPIO_PIN_RESET)
>
>void LED_HC595_138_W(uint8_t duan, uint8_t wei);
>void LED_HC595_595_W(uint8_t duan, uint8_t wei);
>#endif //HAL_SMG_H
>
>```
>
>### C文件
>
>```c
>#include "smg.h"
>
>//共阴数字数组
>const uint8_t smg_num[]={0x3f,0x06,0x5b,0x4f,0x66,0x6d,0x7d,0x07,0x7f,0x6f,0x77,0x7c,0x39,0x5e,0x79,0x71};
>//位选数组
>const uint8_t smg_weis[]={0xfe,0xfd,0xfb,0xf7,0xef,0xdf,0xbf,0x7f};
>//74HC138驱动
>//数码管位选
>//num:要显示的数码管编号 0-7(共8个数码管)
>void HC138_Wei(uint8_t num)
>{
>    if(num&0x01)HC138_A0_H;
>    else HC138_A0_L;
>
>    if(num&0x02)HC138_A1_H;
>    else HC138_A1_L;
>
>    if(num&0x04)HC138_A2_H;
>    else HC138_A2_L;
>}
>/*74HC595驱动,数码管刷新显示*/
>void HC595_Refresh(void)
>{
>    HC595_LCLK_H;
>    Delay_Us(5);
>    HC595_LCLK_L;
>}
>void HC595_Wei(uint8_t num)
>{
>    uint8_t i;
>    uint8_t smg_wei = smg_weis[num];
>    for( i=0;i<8;i++)
>    {
>        if(smg_wei&0x80)HC595_DATA_H;
>        else HC595_DATA_L;
>        HC595_SCLK_L;
>        Delay_Us(5);
>        HC595_SCLK_H;
>        smg_wei<<=1;
>    }
>    HC595_Refresh();
>}
>//74HC595驱动
>void HC595_Write_Data(uint8_t duan)
>{
>    uint8_t i;
>    uint8_t smg_duan = smg_num[duan];
>    for( i=0;i<8;i++)
>    {
>        if(smg_duan&0x80)HC595_DATA_H;
>        else HC595_DATA_L;
>        HC595_SCLK_L;
>        Delay_Us(5);
>        HC595_SCLK_H;
>        smg_duan<<=1;
>    }
>    HC595_Refresh();
>}
>
>/**
>* @brief HC595+HC138显示
>* @param duan: 字符显示(0-F)
>* @param wei: 位选(0-7)
>* @retval None
>*/
>void LED_HC595_138_W(uint8_t duan, uint8_t wei){
>    HC138_Wei(wei);
>    HC595_Write_Data(duan);
>}
>/**
>* @brief 双HC595显示
>* @param duan: 字符显示(0-F)
>* @param wei: 位选(0-7)
>* @retval None
>*/
>void LED_HC595_595_W(uint8_t duan, uint8_t wei){
>    HC595_Wei(wei);
>    HC595_Write_Data(duan);
>}
>```
>
>


## 使用实例

> 1. HC595+HC138
>
>    ```C
>    for(int i=0;i<8;i++){
>    	LED_HC595_138_W(i,i);
>    	osDelay(2);
>    }
>    ```
>
> 2. 双HC595(先选后显)
>
>    ```c
>    for(int i=0;i<8;i++){
>    	LED_HC595_595_W(i,i);
>    	osDelay(2);
>    }
>    ```
>
>    
