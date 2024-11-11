```json
{
    "date":"2024.9.10 10:06",
    "tags": ["BLOG","OLED","STM32","屏幕","驱动"],
    "description": "C/C++跨平台通用屏幕驱动库",
    "author": "JiXieShi",
    "musicId":"5381722575"
}
```

## C/C++跨平台屏幕驱动之OLED

### 库地址

[机械师/HW_Lib](https://gitee.com/JiXieShi_STARSS/hw_-lib)

[JiXieShi/HW_Lib: 自用嵌入式工具库 - HW_Lib - Starss-Git](https://gitea.starss.cc/JiXieShi/HW_Lib)

### 相关代码段

1. 设备结构体

   ```c
   struct OLED_Dev
   {
       uint8_t* buf; /**< 显示缓冲区指针 */
       uint8_t width; /**< 显示宽度 */
       uint8_t height; /**< 显示高度 */
       OLED_STATE_T state; /**< OLED状态 */
       OLED_CMD_t cmd; /**< OLED命令处理函数指针 */
       OLED_DATA_t data; /**< OLED数据处理函数指针 */
   #if REFRESH_CALL_ENABLE
       OLED_REFRESH_t call; /**< OLED刷新函数指针 */
   #endif
   };
   ```

2. 初始化

   ```c
   /**
    * @brief   OLED初始化
    * @param   dev: [输入] OLED设备指针
    * @return  void
    * @example OLED_Init(&oled_dev);
    */
   void OLED_Init(OLED_T *dev) ;
   
   /**
   * @brief   OLED初始化
   * @param   dev: [输入] OLED设备指针
   * @param   cmd: [输入] OLED初始化指令表
   * @param   len: [输入] OLED初始化指令表长度
   * @return  void
   * @example OLED_Init(&oled_dev);
   */
   void OLED_Init_CMD(OLED_T* dev, uint8_t* cmd, uint16_t len);
   ```

### 使用示例

1. PC模拟

   ```c
   uint8_t Cmd(uint8_t *data, size_t l) {
   //    Buf_Print("Cmd", data, l, 16);
   }
   
   uint8_t Data(uint8_t *data, size_t l) {
   //    Buf_Print("Data", data, l, 16);
   }
   //模拟使用整体刷新
   void Refresh_Call(OLED_T *dev) {
   //    LOGT("OLED", "CALL");
   //    Buf_Print("Buf", dev->buf, dev->width * (dev->height / 8), 128);
       SIM_OLED_DrawFromBuffer(dev->buf, dev->width, dev->height / 8);
   }
   
   uint8_t oledbuf[8][128] = {0};//缓冲区初始化
   OLED_T oled = {
           .height=64,
           .width=128,
           .state=IDLE,
           .buf=oledbuf,
           .cmd=Cmd,
           .data=Data,
           .call=Refresh_Call,
   };//设备初始化
   int main(){
       OLED_Init(&oled);
       OLED_CLS(&oled);
       OLED_ShowCHString(&oled, 1, 24, "星海科技机械师");
       OLED_DrawRect(&oled, 0, 0, 127, 63);
       OLED_Refresh(&oled);
       return 0;
   }
   ```

   效果如下

   ![image-20241111134848845](https://s2.loli.net/2024/11/11/tC6dVuMaNrAPncg.png)

2. PC使用CH347驱动

   ```c
   #include <stdio.h>
   #include "CH347DLL.H"
   #include <Windows.h>
   #include "oled.h"
   
   ULONG Index=0;
   
   #define OLED_ADDR 0x78
   #define OLED_CMD  0x00
   #define OLED_DATA 0x40
   uint8_t outbuf[1024];
   
   uint8_t oledbuf[8][128] = {0};
   uint8_t Cmd(uint8_t *data, size_t l) {
       outbuf[0]=OLED_ADDR;
       outbuf[1]=OLED_CMD;
       memcpy(outbuf+2,data,l);
       CH347StreamI2C(Index,l+2,(PVOID)outbuf,0,NULL);
   }
   
   uint8_t Data(uint8_t *data, size_t l) {
       outbuf[0]=OLED_ADDR;
       outbuf[1]= OLED_DATA;
       memcpy(outbuf+2,data,l);
       CH347StreamI2C(Index,l+2,(PVOID)outbuf,0,NULL);
   }
   
   OLED_T oled = {
           .height=64,
           .width=128,
           .state=IDLE,
           .buf=oledbuf,
           .cmd=Cmd,
           .data=Data,
   };
   int main(void) {
       if(CH347OpenDevice(Index)!=INVALID_HANDLE_VALUE){
           CH347SetTimeout(Index, 2000,2000);
           if (CH347I2C_Set(Index, 0b00000011) != 0) {
               printf("设置 I2C 成功\n");
           } else {
               printf("设置 I2C 失败\n");
           }
           if (CH347I2C_SetDelaymS(Index, 1000) != 0) {
               printf("设置 I2C 延时成功\n");
           } else {
               printf("设置 I2C 延时失败\n");
           }
           OLED_Init(&oled);
           OLED_CLS(&oled);
           Sleep(500);
           OLED_ShowCHString(&oled, 1, 24, "星海科技机械师");
           OLED_DrawRect(&oled, 0, 0, 127, 63);
           OLED_Refresh(&oled);
           CH347CloseDevice(Index);
       }
       return 0;
   }
   ```

   效果如下

   ![image-20241111143100333](https://s2.loli.net/2024/11/11/Ux1HmfLpTMZD8j5.png)

3. STM32使用

   1. 平台适配

      oled_port.h

      ```c
      #ifndef KS_OLED_PORT_H
      #define KS_OLED_PORT_H
      
      #include "oled.h"
      extern OLED_T oled;
      void OLED_init();
      #endif //KS_OLED_PORT_H
      ```

      oled_port.c

      ```c
      #include "oled_port.h"
      #include "main.h"
      // #include "spi.h"
      #include "i2c.h"
      #include "tool.h"
      OLED_T oled;
      uint8_t oledbuf[128][8];
      uint8_t Cmd(uint8_t *data, uint32_t l) {
      //    LOGT("OLED", "CMD:%d", l);
      //    Buf_Print("CMD", data, l, 16);
          HAL_I2C_Mem_Write(&hi2c2, 0x78, 0x00, I2C_MEMADD_SIZE_8BIT, data, l, 100);
          HAL_GPIO_WritePin(OLED_DC_GPIO_Port, OLED_DC_Pin, GPIO_PIN_RESET);
          HAL_GPIO_WritePin(OLED_CS_GPIO_Port, OLED_CS_Pin, GPIO_PIN_RESET);
          HAL_SPI_Transmit(&hspi2, data, l, 100);
          HAL_GPIO_WritePin(OLED_CS_GPIO_Port,OLED_CS_Pin,GPIO_PIN_SET);
          HAL_GPIO_WritePin(OLED_DC_GPIO_Port, OLED_DC_Pin, GPIO_PIN_SET);
      }
      
      uint8_t Data(uint8_t *data, uint32_t l) {
      //    LOGT("OLED", "DATA:%d", l);
      //    Buf_Print("DATA", data, l, 16);
          HAL_I2C_Mem_Write(&hi2c2, 0x78, 0x40, I2C_MEMADD_SIZE_8BIT, data, l, 100);
          HAL_GPIO_WritePin(OLED_DC_GPIO_Port, OLED_DC_Pin, GPIO_PIN_SET);
          HAL_GPIO_WritePin(OLED_CS_GPIO_Port, OLED_CS_Pin, GPIO_PIN_RESET);
          HAL_SPI_Transmit(&hspi2, data, l, 100);
          HAL_GPIO_WritePin(OLED_CS_GPIO_Port,OLED_CS_Pin,GPIO_PIN_SET);
          HAL_GPIO_WritePin(OLED_DC_GPIO_Port, OLED_DC_Pin, GPIO_PIN_SET);
      }
      //DMA显示驱动
      //void Call(OLED_T*dev) {
      ////    LOGT("OLED", "CALL:%d", dev->width*dev->height/8);
      ////    Buf_Print("CALL", dev->buf, dev->width*dev->height/8, 16);
      ////    HAL_GPIO_WritePin(OLED_DC_GPIO_Port, OLED_DC_Pin, GPIO_PIN_RESET);
      ////    HAL_GPIO_WritePin(OLED_CS_GPIO_Port, OLED_CS_Pin, GPIO_PIN_RESET);
      ////    HAL_SPI_Transmit(&hspi2, data, l, 0xFF);
      //}
      void OLED_init() {
          oled.buf = oledbuf;
          oled.cmd = Cmd;
          oled.data = Data;
          oled.height = 64;
          oled.width = 128;
      #if REFRESH_CALL_ENABLE
          oled.call=Call;
      #endif
          HAL_GPIO_WritePin(OLED_RST_GPIO_Port, OLED_RST_Pin, GPIO_PIN_RESET);
          HAL_Delay(200);
          HAL_GPIO_WritePin(OLED_RST_GPIO_Port, OLED_RST_Pin, GPIO_PIN_SET);
          OLED_Init(&oled);
      }
      ```
      
   2. 界面文件

      page.h

      ```c
      #ifndef HW_LIB_PAGE_H
      #define HW_LIB_PAGE_H
      
      #include "oled.h"
      
      typedef void (*Page_t)(OLED_T *dev);
      
      extern uint8_t pageid, cur, cnt, item_h, item_w;
      extern uint8_t cnt_f;
      
      typedef enum page_e{
          MAIN_PAGE,
          A_PAGE,
          B_PAGE,
          C_PAGE,
          IMG_PAGE,
          numbe_off_page,
      };
      
      typedef struct Page_L {
          uint8_t id: 4;
          uint8_t curmax: 4;
          uint8_t curmin: 4;
          uint8_t back: 4;
          uint8_t next: 4;
          uint8_t item_h:6;
          uint8_t item_w: 7;
          Page_t page;
      } Page_L_t;
      
      void pageinit();
      
      Page_L_t pagesearch(uint8_t id);
      
      void mainpage(OLED_T *dev);
      
      void pageA(OLED_T *dev);
      
      void pageB(OLED_T *dev);
      
      void pageC(OLED_T *dev);
      void pageImg(OLED_T *dev);
      
      #endif //HW_LIB_PAGE_H
      ```
   
      page.c

      ```c
      #include <stdio.h>
      #include "page.h"
      #include "list.h"
      #include "bmp.h"
      #include "stm32f1xx_hal.h"
      
      uint8_t pageid = 0, cur = 1, cnt = 0, item_h = 16, item_w = 0;
      uint8_t cnt_f=1;
      static List_t list;
      Page_L_t mainp = {MAIN_PAGE, 0, 0, MAIN_PAGE, 1, 16, 16, mainpage};
      Page_L_t ap = {A_PAGE, 2, 1, MAIN_PAGE, 1, 12, 36, pageA};
      Page_L_t bp = {B_PAGE, 2, 1, MAIN_PAGE, 2, 12, 36, pageB};
      Page_L_t cp = {C_PAGE, 2, 1, MAIN_PAGE, 3, 12, 36, pageC};
      Page_L_t ip = {IMG_PAGE, 0, 0, MAIN_PAGE, 4, 0, 0, pageImg};
      
      void pageinit() {
          list_init(&list);
          list_insert(&list, &mainp);
          list_insert(&list, &ap);
          list_insert(&list, &bp);
          list_insert(&list, &cp);
          list_insert(&list, &ip);
      }
      
      int compare_page(const void *s1, const void *s2) {
          Page_L_t *data1 = (Page_L_t *) s1;
          uint8_t *data2 = (uint8_t *) s2;
      
          return (data1->id - *data2);
      }
      
      Page_L_t pagesearch(uint8_t id) {
          Page_L_t *ret;
          ret = list_search(&list, &id, compare_page);
          return *ret;
      }
      
      void pagecur(OLED_T *dev) {
          if (cnt % 2) {
              if (pagesearch(pageid).curmin != pagesearch(pageid).curmax&&pagesearch(pageid).item_h != 0) {
                  OLED_ShowString(dev, item_w - item_h, cur * item_h, ">", item_h);
              }
          }
          if(cnt_f){
              cnt++;
          }
      }
      
      void mainpage(OLED_T *dev) {
          OLED_CLS(dev);
          OLED_ShowCHString(dev, 32, 0 * item_h, "主界面");
          OLED_ShowCHString(dev, 12, 1 * item_h, "信息显示");
          if(cnt%2==0){
              OLED_ShowCHString(dev, 12, 2 * item_h, "信息1");
          } else{
              OLED_ShowCHString(dev, 6, 2 * item_h, "信息2");
          }
          OLED_ShowCHString(dev, 12, 3 * item_h, "尾注");
          pagecur(dev);
      }
      
      void pageA(OLED_T *dev) {
          OLED_CLS(dev);
          OLED_ShowString(dev, 32, 0, " A  Page", 12);
          OLED_ShowString(dev, 36, 12, "1.contx", 12);
          OLED_ShowString(dev, 36, 24, "         back", 12);
          pagecur(dev);
      }
      
      void pageB(OLED_T *dev) {
          OLED_CLS(dev);
          OLED_ShowString(dev, 32, 0, " B  Page", 12);
          OLED_ShowString(dev, 36, 12, "1.contx", 12);
          OLED_ShowString(dev, 36, 24, "         back", 12);
          pagecur(dev);
      }
      
      void pageC(OLED_T *dev) {
          OLED_CLS(dev);
          OLED_ShowString(dev, 32, 0, " C  Page", 12);
          OLED_ShowString(dev, 36, 12, "1.contx", 12);
          OLED_ShowString(dev, 36, 24, "         back", 12);
          pagecur(dev);
      }
      
      void pageImg(OLED_T *dev) {
          OLED_CLS(dev);
          switch (cnt % 6) {
              case 0:
                  OLED_ShowPic(dev, 0, 0, 64, 64, BMP1);
                  break;
              case 1:
                  OLED_ShowPic(dev, 0, 0, 64, 64, BMP2);
                  break;
              case 2:
                  OLED_ShowPic(dev, 0, 0, 64, 64, BMP3);
                  break;
              case 3:
                  OLED_ShowPic(dev, 0, 0, 64, 64, BMP4);
                  break;
              case 4:
                  OLED_ShowPic(dev, 0, 0, 64, 64, BMP5);
                  break;
              case 5:
                  OLED_ShowPic(dev, 0, 0, 64, 64, BMP6);
                  break;
              default:
                  break;
          }
          pagecur(dev);
      }
      ```
      
      效果如下
      
      ![image-20241111135412516](https://s2.loli.net/2024/11/11/qR1E7Bb9jVXlHdP.png)

