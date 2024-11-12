```json
{
    "date":"2024.9.12 10:06",
    "tags": ["BLOG","OLED","STM32","屏幕","驱动"],
    "description": "C/C++跨平台通用TFT屏幕驱动库",
    "author": "JiXieShi",
    "musicId":"5381722575"
}
```

## C/C++跨平台屏幕驱动之TFT

### 库地址

- [机械师/HW_Lib](https://gitee.com/JiXieShi_STARSS/hw_-lib)
- [JiXieShi/HW_Lib: 自用嵌入式工具库 - HW_Lib - Starss-Git](https://gitea.starss.cc/JiXieShi/HW_Lib)

### 相关代码段

1. 接口定义

   ```c
   /**
    * @brief   写寄存器数据函数指针类型
    * @param   reg: [输入]     寄存器地址
    * @param   pdata: [输入]   数据缓冲区指针
    * @param   ldata: [输入]   数据长度
    * @return  uint8_t         返回状态
    * @example write_status = TFT_WRITE_REG(reg_addr, data_buffer, data_length);
    **/
   typedef uint8_t (*TFT_WRITE_REG_t)(uint8_t reg, uint8_t *pdata, size_t ldata);
   
   /**
    * @brief   读寄存器数据函数指针类型
    * @param   reg: [输入]     寄存器地址
    * @param   pdata: [输出]   数据缓冲区指针
    * @param   ldata: [输入]   数据长度
    * @return  uint8_t         返回状态
    * @example read_status = TFT_READ_REG(reg_addr, data_buffer, data_length);
    **/
   typedef uint8_t (*TFT_READ_REG_t)(uint8_t reg, uint8_t *pdata, size_t ldata);
   
   /**
    * @brief   发送数据函数指针类型
    * @param   data: [输入]    数据缓冲区指针
    * @param   len: [输入]     数据长度
    * @return  uint8_t         返回状态
    * @example send_status = TFT_SEND_DATA(data_buffer, data_length);
    **/
   typedef uint8_t (*TFT_SEND_DATA_t)(uint8_t *data, size_t len);
   
   /**
    * @brief   接收数据函数指针类型
    * @param   data: [输出]    数据缓冲区指针
    * @param   len: [输入]     数据长度
    * @return  uint8_t         返回状态
    * @example recv_status = TFT_RECV_DATA(data_buffer, data_length);
    **/
   typedef uint8_t (*TFT_RECV_DATA_t)(uint8_t *data, size_t len);
   
   /**
    * @brief   TFT背光控制函数指针类型
    * @param   data: [输入] 数据
    * @return  uint8_t 返回值
    */
   typedef uint8_t (*TFT_BLACKLIGHT_t)(uint8_t data);
   
   /**
    * @brief   TFT延迟函数指针类型
    * @param   ms: [输入] 毫秒延迟
    * @return  uint8_t 返回值
    */
   typedef void (*TFT_DELAY_t)(uint32_t ms);
   ```

2. 类型定义

   ```c
   /**
   * @brief   TFT状态枚举
   */
   typedef enum {
       TFT_ERROR,/**< 错误状态 */
       TFT_IDLE, /**< 空闲状态 */
       TFT_WRITE, /**< 写入状态 */
       TFT_REFRESH, /**< 刷新状态 */
   } TFT_STATE_T;
   
   typedef enum {
       LONGITUDINAL = 0x01U,/* 液晶屏纵向选择 */
       LONGITUDINAL_180,/* 液晶屏纵向旋转 180° 方向选择 */
       HORIZONTAL,/* 液晶屏横向选择 */
       HORIZONTAL_180,/* 液晶屏横向旋转 180° 方向选择 */
   } TFT_DIR_T;
   
   typedef union TFT_Color {
       uint16_t color;
       uint16_t u16;
       uint8_t u8[2];
   } TFT_Color_t;
   
   /**
    * @brief   TFT设备结构体
    */
   struct TFT_Dev {
       uint32_t id;                   /**< TFT设备ID */
       uint8_t setxcmd;               /**< 设置X坐标命令 */
       uint8_t setycmd;               /**< 设置Y坐标命令 */
       uint8_t wgramcmd;              /**< 写GRAM命令 */
       TFT_DIR_T dir: 4;              /**< 显示方向，占4位 */
       TFT_STATE_T state: 4;          /**< TFT状态，占4位 */
       uint16_t width;                /**< 显示宽度 */
       uint16_t height;               /**< 显示高度 */
       TFT_WRITE_REG_t writeReg;      /**< 写寄存器函数指针 */
       TFT_READ_REG_t readReg;        /**< 读寄存器函数指针 */
       TFT_SEND_DATA_t sendData;      /**< 发送数据函数指针 */
       TFT_RECV_DATA_t recvData;      /**< 接收数据函数指针 */
       TFT_BLACKLIGHT_t blacklight;   /**< TFT背光控制函数指针 */
       TFT_DELAY_t delay;             /**< TFT延迟函数指针类型 */
   #if REFRESH_CALL_ENABLE
       TFT_REFRESH_t call;            /**< TFT刷新函数指针，仅在REFRESH_CALL_ENABLE宏定义启用时有效 */
   #endif
   };
   ```

### 平台示例

1. PC模拟

   ```c
   //硬件实现区
   uint8_t tft_writereg(uint8_t reg, uint8_t *pdata, size_t ldata) {
       uint8_t result;
       fill = false;
       Data16_t data16;
       if (reg == SETXCMD) {
           if (ldata >= 4) {
               data16.u8[1] = *pdata;
               pdata++;
               data16.u8[0] = *pdata;
               pdata++;
               xs = data16.u16;
               data16.u8[1] = *pdata;
               pdata++;
               data16.u8[0] = *pdata;
               xe = data16.u16;
           } else if (ldata >= 2) {
               data16.u8[1] = *pdata;
               pdata++;
               data16.u8[0] = *pdata;
               xs = data16.u16;
           }
   //        LOGT("SETX","X_S:%d,X_E:%d,len:%d",xs,xe,ldata);
       } else if (reg == SETYCMD) {
           if (ldata >= 4) {
               data16.u8[1] = *pdata;
               pdata++;
               data16.u8[0] = *pdata;
               pdata++;
               ys = data16.u16;
               data16.u8[1] = *pdata;
               pdata++;
               data16.u8[0] = *pdata;
               ye = data16.u16;
           } else if (ldata >= 2) {
               data16.u8[1] = *pdata;
               pdata++;
               data16.u8[0] = *pdata;
               ys = data16.u16;
           }
   //        LOGT("SETY","Y_S:%d,Y_E:%d,len:%d",ys,ye,ldata);
       } else if (reg == WGRAM_CMD) {
           fill = true;
       }
       if (result > 0) {
           result = -1;
       } else {
           result = 0;
       }
       return result;
   }
   
   //硬件实现
   uint8_t tft_senddata(uint8_t *data, size_t len) {
       uint8_t result;
       uint32_t color[len / 2];
       TFT_Color_t color_u;
       if (!fill || len == 0)return -1;
       if (len == 2) {
           color_u.u8[1] = *data;
           color_u.u8[0] = *data++;
           SIM_Color_DrawPiexl(&tft_display, (SIM_Color_t) RGB565_to_ARGB8888(color_u.u16, true), xs, ys);
   //        LOGT("Piexl","color:%x,x:%d,y:%d,len:%d",color_u.u16,xs,ys,len);
   //        SIM_Color_ImgFromBuffer(color,xs, ys, uint16_t width, 1)
       } else {
           for (int i = 0; i < len / 2; i++) {
               color_u.u8[1] = *data;
               data++;
               color_u.u8[0] = *data;
               data++;
               color[i] = RGB565_to_ARGB8888(color_u.u16, true);
           }
           SIM_Color_ImgFromBuffer(&tft_display, color, xs, ys, len / 2 - 1, 1);
   //        SIM_Color_DrawHLineBuffer(color, xs, ys, len / 2);
   //        LOGT("Img","x:%d,y:%d,len:%d",xs,ys,len);
       }
   
       if (result > 0) {
           result = -1;
       } else {
           result = 0;
       }
       return result;
   }
   
   int Test_tft(void *arg) {
       //设备信息预填充
       demo_tft.width = 480;//实际如有支持不用填(如ST7735/7796)
       demo_tft.height = 320;//实际如有支持不用填(如ST7735/7796)
       demo_tft.wgramcmd = WGRAM_CMD;//实际如有支持不用填(如ST7735/7796)
       demo_tft.setycmd = SETYCMD;//实际如有支持不用填(如ST7735/7796)
       demo_tft.setxcmd = SETXCMD;//实际如有支持不用填(如ST7735/7796)
       demo_tft.writeReg = tft_writereg;//必须实现
       demo_tft.sendData = tft_senddata;//必须实现
       demo_tft.dir = HORIZONTAL;//必填
   
       //模拟初始化
       SIM_Display_Init("TFT", demo_tft.width, demo_tft.height, 0xFFFF, 0x0000, 2, &tft_display);
   
       TFT_Init(&demo_tft);//初始化
       TFT_Color_t t;
       t.color = 0XFFFF;
       TFT_Fill(&demo_tft, 0, 0, demo_tft.width, demo_tft.height, t);
   
       t.color = 0XFFFF;
       TFT_SetColor(0xFFFF, 0x0000);
   
       TFT_Fill(&demo_tft, 0, 0, demo_tft.width, demo_tft.height, t);
   
       TFT_SetColor(0x7D7C, 0x0000);
       TFT_DrawRect(&demo_tft, 0, 0, 50, 50);
   
       t.color = 0X01CF;
       TFT_Fill(&demo_tft, 1, 1, 49, 49, t);
   
       TFT_SetColor(0x7D7C, 0x0000);
       TFT_ShowCHString(&demo_tft, 0, 60, "星海科技机械师", 1);
   
       TFT_DrawCircle(&demo_tft, 25, 25, 15);
       TFT_DrawRoundedRect(&demo_tft,200,0,100,30,8);
       TFT_DrawArc(&demo_tft,200,50,30,0,360);
       TFT_DrawCross(&demo_tft, 25, 25, 10);
       TFT_DrawXCross(&demo_tft, 25, 25, 10);
       
       TFT_ShowString(&demo_tft, 60, 20, "JiXieShi", 16, 1);
   
       TFT_ShowString(&demo_tft, 0, 160,
                      "abcdefghigklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ<>(){}[]\\/?.,;:'\"!@#$%^&*-=_+~|", 16, 1);
       for (float p = 0; p < 1; p += 0.001) {
           TFT_ShowBar(&demo_tft, 0, 100, demo_tft.width, 24, p);
           TFT_ShowBar(&demo_tft, 0, 125, demo_tft.width, 16, p);
           TFT_ShowBar(&demo_tft, 0, 142, demo_tft.width, 12, p);
           TFT_ShowBar(&demo_tft, 0, 155, demo_tft.width, 3, p);
       }
       SIM_Display_STOP(&tft_display);
       return 0;
   }
   ```

   效果如下![image-20241112223044103](https://s2.loli.net/2024/11/12/S6fBq8baWniX5Yy.png)

2. STM 双屏异显

   ```c
   #define TFT_RS_SET      HAL_GPIO_WritePin(TFT_DC_GPIO_Port,TFT_DC_Pin,GPIO_PIN_SET)//PC4
   #define TFT_RS_RESET    HAL_GPIO_WritePin(TFT_DC_GPIO_Port,TFT_DC_Pin,GPIO_PIN_RESET)
   //LCD_CS
   #define TFT_CS_SET      HAL_GPIO_WritePin(TFT_CS_GPIO_Port,TFT_CS_Pin,GPIO_PIN_SET)
   #define TFT_CS_RESET    HAL_GPIO_WritePin(TFT_CS_GPIO_Port,TFT_CS_Pin,GPIO_PIN_RESET)
   
   TFT_T st7735;
   TFT_T st7796;
   
   uint8_t tft_writereg(uint8_t reg,uint8_t* pdata,size_t ldata){
       uint8_t result;
       TFT_CS_RESET;
       TFT_RS_RESET;
       result = HAL_SPI_Transmit(&hspi4,&reg,1,100);
       TFT_RS_SET;
       if(ldata > 0)
           result += HAL_SPI_Transmit(&hspi4,pdata,ldata,500);
       TFT_CS_SET;
       if(result>0){
           result = -1;}
       else{
           result = 0;}
       return result;
   }
   uint8_t tft_readreg(uint8_t reg,uint8_t* pdata,size_t ldata){
       uint8_t result;
       TFT_CS_RESET;
       TFT_RS_RESET;
       result = HAL_SPI_Transmit(&hspi4,&reg,1,100);
       TFT_RS_SET;
       result += HAL_SPI_Receive(&hspi4,pdata,ldata,500);
       TFT_CS_SET;
       if(result>0){
           result = -1;}
       else{
           result = 0;}
       return result;
   }
   uint8_t tft_readdata(uint8_t *data,size_t len){
       uint8_t result;
       TFT_CS_RESET;
       //TFT_RS_SET;
       result = HAL_SPI_Receive(&hspi4,data,len,500);
       TFT_CS_SET;
       if(result>0){
           result = -1;}
       else{
           result = 0;}
       return result;
   }
   uint8_t tft_senddata(uint8_t *data,size_t len){
       uint8_t result;
       TFT_CS_RESET;
       //TFT_RS_SET;
       result =HAL_SPI_Transmit(&hspi4,data,len,100);
       TFT_CS_SET;
       if(result>0){
           result = -1;}
       else{
           result = 0;}
       return result;
   }
   
   uint8_t lcd_writereg(uint8_t reg,uint8_t* pdata,size_t ldata){
       uint8_t result;
       LCD_CS_RESET;
       LCD_DC_RESET;
       result = HAL_SPI_Transmit(&hspi6,&reg,1,100);
       LCD_DC_SET;
       if(ldata > 0)
           result += HAL_SPI_Transmit(&hspi6,pdata,ldata,500);
       LCD_CS_SET;
       if(result>0){
           result = -1;}
       else{
           result = 0;}
       return result;
   }
   uint8_t lcd_readreg(uint8_t reg,uint8_t* pdata,size_t ldata){
       uint8_t result;
       LCD_CS_RESET;
       LCD_DC_RESET;
       result = HAL_SPI_Transmit(&hspi6,&reg,1,100);
       LCD_DC_SET;
       result += HAL_SPI_Receive(&hspi6,pdata,ldata,500);
       LCD_CS_SET;
       if(result>0){
           result = -1;}
       else{
           result = 0;}
       return result;
   }
   uint8_t lcd_readdata(uint8_t *data,size_t len){
       uint8_t result;
       LCD_CS_RESET;
       //LCD_DC_SET;
       result = HAL_SPI_Receive(&hspi6,data,len,500);
       LCD_CS_SET;
       if(result>0){
           result = -1;}
       else{
           result = 0;}
       return result;
   }
   uint8_t lcd_senddata(uint8_t *data,size_t len){
       uint8_t result;
       LCD_CS_RESET;
       //LCD_DC_SET;
       result =HAL_SPI_Transmit(&hspi6,data,len,100);
       LCD_CS_SET;
       if(result>0){
           result = -1;}
       else{
           result = 0;}
       return result;
   }
   
   int main(){
   	 st7735.sendData=tft_senddata;
       st7735.recvData=tft_readdata;
       st7735.writeReg=tft_writereg;
       st7735.readReg=tft_readreg;
       st7735.dir=LONGITUDINAL;
       st7735.delay=HAL_Delay;
       st7735.id=TFT_ST7735;
   //
       st7796.sendData=lcd_senddata;
       st7796.recvData=lcd_readdata;
       st7796.writeReg=lcd_writereg;
       st7796.readReg=lcd_readreg;
       st7796.dir=LONGITUDINAL;
       st7796.delay=HAL_Delay;
       st7796.id=TFT_ST7796;
   //
       TFT_Color_t t;t.color=0X0000;
       TFT_Init(&st7735);
   	TFT_Init(&st7796);
       
       t.color=0XFFFF;
       TFT_SetColor(0xFFFF,0x0000);
       TFT_DrawRect(&st7735,0,0,50,50);
       TFT_Fill(&st7735,0,0,st7735.width,st7735.height,t);
   	TFT_Fill(&st7796,0,0,st7796.width,st7796.height,t);
   
       TFT_SetColor(0x07E0,0x0000);
       TFT_DrawRect(&st7735,0,0,50,50);
   	TFT_DrawRect(&st7796,0,0,50,50);
   
       t.color=0X01CF;
       TFT_Fill(&st7735,1,1,49,49,t);
   	TFT_Fill(&st7796,1,1,49,49,t);
       TFT_SetColor(0x7D7C,0x0000);
       TFT_ShowCHString(&st7735,0,60,"星海科技机械师",1);
   	TFT_ShowCHString(&st7796,0,60,"星海科技机械师",1);
       TFT_DrawCircle(&st7735,25,25,10);
   	TFT_DrawCircle(&st796,25,25,10);
   
       TFT_DrawCross(&st7735,25,25,10);
   	TFT_DrawCross(&st7796,25,25,10);
   
       TFT_ShowString(&st7735, 60, 20, "jxs", 16,1);
   	TFT_ShowString(&st776, 60, 20, "jxs", 16,1);
       TFT_ShowString(&st7735, 0, 160, "abcdefghigklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ<>(){}[]\\/?.,;:'\"!@#$%^&*-=_+~|", 16,1);
       TFT_ShowString(&st7796, 0, 160, "abcdefghigklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ<>(){}[]\\/?.,;:'\"!@#$%^&*-=_+~|", 16,1);
       float p=0;
       TFT_ShowBar(&st7735,0,100,st7735.width,5,p);
   }
   ```

   效果如下![Cache_4b949c54e6743afd](https://s2.loli.net/2024/11/12/IC2Kf6dvS7mRjyB.jpg)