键盘鼠标二合一工程

# 1.键盘报文

```
键盘发送给PC的数据每次8个字节：BYTE1 BYTE2 BYTE3 BYTE4 BYTE5 BYTE6 BYTE7 BYTE8。定义分别是：
BYTE1 --
|--bit0: Left Control 是否按下，按下为1
|--bit1: Left Shift 是否按下，按下为1
|--bit2: Left Alt 是否按下，按下为1
|--bit3: Left GUI 是否按下，按下为1
|--bit4: Right Control 是否按下，按下为1
|--bit5: Right Shift 是否按下，按下为1
|--bit6: Right Alt 是否按下，按下为1
|--bit7: Right GUI 是否按下，按下为1
BYTE2 -- 暂不清楚，有的地方说是保留位
BYTE3--BYTE8 -- 这六个为普通按键
```



# 2、鼠标报文

```
鼠标发送给PC的数据每次4个字节
BYTE1 BYTE2 BYTE3 BYTE4
定义分别是：
BYTE1 --
       |--bit7:   1   表示   Y   坐标的变化量超出－256   ~   255的范围,0表示没有溢出
       |--bit6:   1   表示   X   坐标的变化量超出－256   ~   255的范围，0表示没有溢出
       |--bit5:   Y   坐标变化的符号位，1表示负数，即鼠标向下移动
       |--bit4:   X   坐标变化的符号位，1表示负数，即鼠标向左移动
       |--bit3:     恒为1
       |--bit2:     1表示中键按下
       |--bit1:     1表示右键按下
       |--bit0:     1表示左键按下
BYTE2 -- X坐标变化量，与byte的bit4组成9位符号数,负数表示向左移，正数表右移。用补码表示变化量
BYTE3 -- Y坐标变化量，与byte的bit5组成9位符号数，负数表示向下移，正数表上移。用补码表示变化量
BYTE4 -- 滚轮变化。0x01表示滚轮向前滚动一格；0xFF表示滚轮向后滚动一格；0x80是个中间值，不滚动。
```

# 4.键盘鼠标API

## 1.1函数USBD_HID_SendReport

| 函数名    | USBD_HID_SendReport                                          |
| --------- | ------------------------------------------------------------ |
| 函数原型  | uint8_t USBD_HID_SendReport(USBD_HandleTypeDef  *pdev, uint8_t *report, uint16_t len) |
| 功能描述  | 发送鼠标或键盘命令                                           |
| 输入参数1 | 这是一个指向USB设备句柄结构体的指针。该结构体包含了设备的所有状态和配置信息。通过该指针，函数可以访问和操作USB设备的状态 |
| 输入参数2 | 这是一个指向需要发送的HID报告数据的指针。HID报告是一种特定格式的数据，用于描述设备状态，例如鼠标移动、按键状态等 |
| 输入参数3 | 这是一个16位的无符号整数，用于指定报告的长度，也就是需要发送的数据的字节数 |

> 例如：

```c

键盘发送给PC的数据每次8个字节：BYTE1 BYTE2 BYTE3 BYTE4 BYTE5 BYTE6 BYTE7 BYTE8。定义分别是：
BYTE1 --
|--bit0: Left Control 是否按下，按下为1
|--bit1: Left Shift 是否按下，按下为1
|--bit2: Left Alt 是否按下，按下为1
|--bit3: Left GUI 是否按下，按下为1
|--bit4: Right Control 是否按下，按下为1
|--bit5: Right Shift 是否按下，按下为1
|--bit6: Right Alt 是否按下，按下为1
|--bit7: Right GUI 是否按下，按下为1
BYTE2 -- 暂不清楚，有的地方说是保留位
BYTE3--BYTE8 -- 这六个为普通按键
//发送键盘数据 按下a键盘
uint8_t keyboard[8] = { 0, 0, 04, 0, 0, 0, 0, 0}
USBD_HID_SendReport(&hUsbDeviceFS,(uint8_t*)&keyboard,sizeof(keyboard));

鼠标发送给PC的数据每次4个字节
BYTE1 BYTE2 BYTE3 BYTE4
定义分别是：
BYTE1 --
       |--bit7:   1   表示   Y   坐标的变化量超出－256   ~   255的范围,0表示没有溢出
       |--bit6:   1   表示   X   坐标的变化量超出－256   ~   255的范围，0表示没有溢出
       |--bit5:   Y   坐标变化的符号位，1表示负数，即鼠标向下移动
       |--bit4:   X   坐标变化的符号位，1表示负数，即鼠标向左移动
       |--bit3:     恒为1
       |--bit2:     1表示中键按下
       |--bit1:     1表示右键按下
       |--bit0:     1表示左键按下
BYTE2 -- X坐标变化量，与byte的bit4组成9位符号数,负数表示向左移，正数表右移。用补码表示变化量
BYTE3 -- Y坐标变化量，与byte的bit5组成9位符号数，负数表示向下移，正数表上移。用补码表示变化量
BYTE4 -- 滚轮变化。
//鼠标向右移动10个坐标点
uint8_t mouse[4] = {0, 0, 0a, 0}
USBD_HID_SendReport(&hUsbDeviceFS,(uint8_t*)&mouse,sizeof(mouse));
```
