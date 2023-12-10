```json
{
    "date":"2022.06.20 13:06",
    "tags": ["BLOG","F103C8T6","C8T6","HAL","矩阵键盘"],
    "description": "基于HAL库的矩阵键盘扫描",
    "author": "JiXieShi",
    "musicId":"5381722575"
}
```

## 头文件

```c
#ifndef HAL_KEY_H
#define HAL_KEY_H
//长按次数计数
#define KEY_LONGCNT 50

//IO定义区 Hx上拉 Lx推挽
//可增减 H和L的数量组成不同的矩阵扫描
#define H1 HAL_GPIO_ReadPin(H1_GPIO_Port,H1_Pin)
#define H2 HAL_GPIO_ReadPin(H2_GPIO_Port,H2_Pin)
#define H3 HAL_GPIO_ReadPin(H3_GPIO_Port,H3_Pin)
#define H4 HAL_GPIO_ReadPin(H4_GPIO_Port,H4_Pin)
#define L1_H HAL_GPIO_WritePin(L1_GPIO_Port,L1_Pin,GPIO_PIN_SET)
#define L2_H HAL_GPIO_WritePin(L2_GPIO_Port,L2_Pin,GPIO_PIN_SET)
#define L3_H HAL_GPIO_WritePin(L3_GPIO_Port,L3_Pin,GPIO_PIN_SET)
#define L4_H HAL_GPIO_WritePin(L4_GPIO_Port,L4_Pin,GPIO_PIN_SET)
#define L1_L HAL_GPIO_WritePin(L1_GPIO_Port,L1_Pin,GPIO_PIN_RESET)
#define L2_L HAL_GPIO_WritePin(L2_GPIO_Port,L2_Pin,GPIO_PIN_RESET)
#define L3_L HAL_GPIO_WritePin(L3_GPIO_Port,L3_Pin,GPIO_PIN_RESET)
#define L4_L HAL_GPIO_WritePin(L4_GPIO_Port,L4_Pin,GPIO_PIN_RESET)
//////////////////////////////////////////
extern uint8_t Key_Flag;
extern uint8_t Key_Long_F;
extern uint8_t Key_Treat(void);
#endif //HAL_KEY_H

```

## 源文件

```c
#include "main.h"
#include "key.h"
/**********************
H1-------上拉
H1-------上拉
H1-------上拉
H1-------上拉
L1-------推挽
L2-------推挽
L3-------推挽
L4-------推挽
***********************/
uint8_t Key_Flag = 0;
void Key_Get(uint8_t l){
    switch(l){
        case 1:
            L1_L;
            break;
        case 2:
            L2_L;
            break;
        case 3:
            L3_L;
            break;
        case 4:
            L4_L;
            break;
        default:
            break;

    }
}
void Set_Key_L(void){
    L1_H;
    L2_H;
    L3_H;
    L4_H;
}
void RSet_Key_L(void){
    L1_L;
    L2_L;
    L3_L;
    L4_L;
}
void Key_Treat(void)
{
    Key_Flag = 0;
    for (uint8_t i = 1; i < 5; i++)
    {
        Set_Key_L();
        Key_Get(i);
        if (H1 == 0)
        {
            switch (i)
            {
                case (1):
                    Key_Flag = '1';
                    break;
                case (2):
                    Key_Flag = '2';
                    break;
                case (3):
                    Key_Flag = '3';
                    break;
                case (4):
                    Key_Flag = 'A';
                    break;
                default:
                    Key_Flag=0;
                    break;
            }
        }
        else if (H2 == 0)
        {
            switch (i)
            {
                case (1):
                    Key_Flag = '4';
                    break;
                case (2):
                    Key_Flag = '5';
                    break;
                case (3):
                    Key_Flag = '6';
                    break;
                case (4):
                    Key_Flag = 'B';
                    break;
                default:
                    Key_Flag=0;
                    break;
            }
        }
        else if (H3 == 0)
        {
            switch (i)
            {
                case (1):
                    Key_Flag = '7';
                    break;
                case (2):
                    Key_Flag = '8';
                    break;
                case (3):
                    Key_Flag = '9';
                    break;
                case (4):
                    Key_Flag = 'C';
                    break;
                default:
                    Key_Flag=0;
                    break;
            }
        }
        else if (H4 == 0)
        {
            switch (i)
            {
                case (1):
                    Key_Flag = '*';
                    break;
                case (2):
                    Key_Flag = '0';
                    break;
                case (3):
                    Key_Flag = '#';
                    break;
                case (4):
                    Key_Flag = 'D';
                    break;
                default:
                    Key_Flag=0;
                    break;
            }
        }
    }
    RSet_Key_L();
    if(Key_Value==Key_Flag&&Key_Value!=0){
        Key_Long_Cnt++;
    }else{
        Key_Long_Cnt=0;
    }
    if(Key_Long_Cnt>=KEY_LONGCNT){Key_Long_F=1;}
    else {Key_Long_F=0;}
    Key_Flag=Key_Value;
    return Key_Value;
}

```

## 实例

```c
Key_Treat();
if(Key_Flag=='B'){
	HAL_GPIO_TogglePin(LED_GPIO_Port,LED_Pin);
}
osDelay(50);
```

