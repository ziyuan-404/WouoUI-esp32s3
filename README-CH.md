# **WouoUI for ESP32-S3 (128x128)**

[English Version](https://www.google.com/search?q=README_English_NoEmoji.md)

这是一个基于 [RQNG/WouoUI](https://github.com/RQNG/WouoUI) 的 **ESP32-S3** 移植版本。本项目模仿了稚晖君 MonoUI 风格，实现了一套超丝滑的单色 OLED 菜单 UI，并使用 EC11 旋转编码器进行交互控制。

当前版本主要针对 **128x128 分辨率** 的 OLED 屏幕进行了完整适配和优化。

## **移植说明与特性**

本项目在原版优秀的 UI 框架基础上，针对 ESP32-S3 的硬件特性进行了如下适配：

* **硬件 SPI 加速**：使用 ESP32-S3 的硬件 SPI 驱动 OLED（总线频率设为 10MHz），确保 128x128 屏幕下的超高刷新率和丝滑动画。  
* **非易失性存储适配**：重写了参数存储逻辑，使用 ESP32 的 EEPROM.commit() 机制实现断电参数保存。  
* **中断优化**：针对 ESP32-S3 架构，为 EC11 编码器的中断处理函数添加了 IRAM\_ATTR 属性，防止由于放入 Flash 导致的 Crash 问题。  
* **开发环境**：整体迁移至 **PlatformIO** 平台，依赖管理更加现代化，一键编译烧录。  
* **继承原版所有核心 UI 功能**：  
  * 非线性平滑缓动动画（列表、弹窗、进度条、磁贴图标）。  
  * 白天/黑暗模式自由切换。  
  * 独立弹窗模块，支持背景虚化。  
  * 菜单循环滚动、动画速度全参数可调。

## **硬件接线指南**

本项目默认使用 **ESP32-S3**，硬件接线请参考以下配置（定义在 lib/WouoUI/WouoUI.h 中）：

### **OLED 屏幕 (SPI 接口)**

| OLED 引脚 | ESP32-S3 引脚 | 说明 |
| :---- | :---- | :---- |
| **SCL (CLK)** | GPIO 15 | 硬件 SPI 时钟线 |
| **SDA (MOSI)** | GPIO 7 | 硬件 SPI 数据线 |
| **RES (RST)** | GPIO 4 | 复位引脚 |
| **DC** | GPIO 6 | 数据/命令选择引脚 |
| **CS** | GPIO 5 | 片选引脚 |

### **EC11 旋转编码器**

| EC11 引脚 | ESP32-S3 引脚 | 说明 |
| :---- | :---- | :---- |
| **A (CLK)** | GPIO 18 | 旋转触发引脚，已开启外部中断 |
| **B (DT)** | GPIO 8 | 旋转方向引脚 |
| **SW (BTN)** | GPIO 17 | 按键引脚，已开启内部上拉 |

**注意：** 电压测量页面的 ADC 引脚目前配置为部分默认引脚，实际使用中请根据 ESP32-S3 的 ADC 限制（如开启 WiFi 时 ADC2 不可用）在 WouoUI\_Data.h 和 WouoUI.cpp 中自行重映射。

## **快速开始**

本项目使用 **PlatformIO** 进行开发。

1. 安装 [VSCode](https://code.visualstudio.com/) 以及 [PlatformIO 插件](https://platformio.org/)。  
2. 克隆或下载本项目源码并在 VSCode 中打开。  
3. PlatformIO 会自动读取 platformio.ini 并下载 U8g2 依赖库。  
4. 将 ESP32-S3 连接至电脑，点击底部的 **Upload** 按钮（或使用快捷键 Ctrl+Alt+U）编译并烧录程序。

## **操作说明**

* **睡眠模式**：程序初始默认进入睡眠模式。  
* **唤醒与进入菜单**：在睡眠模式下，**长按**旋钮即可进入主菜单。  
* **菜单导航**：  
  * **旋转**：切换选中项。  
  * **短按**：进入选中项 / 确认修改。  
  * **长按**：返回上一级菜单。  
* **参数保存**：在设置页面修改参数后，退回至睡眠模式时，系统会自动将参数写入 EEPROM 中保存。

## **当前移植进度与已知限制**

* \[x\] 核心 UI 框架移植（列表、磁贴、弹窗）  
* \[x\] 128x128 OLED 硬件 SPI 适配  
* \[x\] 编码器消抖与中断读取优化  
* \[x\] EEPROM 参数存储适配  
* \[ \] **USB HID 功能**：ESP32-S3 支持原生 USB，但当前代码中的键盘快捷键/多媒体控制（如调音量、模拟按键）暂未接入 USBHID 库，如需使用需自行补充 Keyboard 等相关调用。

## **致谢与参考**

* **原版 UI 项目**：[RQNG/WouoUI](https://github.com/RQNG/WouoUI) (非常感谢原作者 RQNG 提供的优秀算法和 UI 设计)  
* **U8g2 图形库**：[olikraus/u8g2](https://github.com/olikraus/u8g2)  
* **UI 灵感来源**：稚晖君的 MonoUI / UltraLink 等设计。

由于原项目不设置开源协议，本项目也暂无特殊开源协议限制。如您有需要，可自由发挥修改。请使用者注意计算机安全。