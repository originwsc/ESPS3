/**
 ***************************************************************************************************
 * 实验简介
 * 实验名称：FLASH模拟U盘 实验
 * 实验平台：正点原子 ESP32-S3 开发板
 * 实验目的：学习ESP32-S3的USB MSC功能使用,实现对FLASH模拟U盘的读写
 * 
 ***************************************************************************************************
 * 硬件资源及引脚分配
 * 1 LED
     LED - IO1
 * 2 XL9555
     IIC_SCL - IO42
     IIC_SDA - IO41
 * 3 正点原子1.3/2.4寸SPILCD模块
 * 
 ***************************************************************************************************
 * 实验现象
 * 1 USB线插入ESP32-S3开发板USB端口后，系统把storage分区模拟成U盘，大家可在电脑上看到U盘的磁盘显示。
 * 2 LED闪烁，指示程序正在运行
 * 
 ***************************************************************************************************
 * 注意事项
 * USART1的通讯波特率为115200
 * 请使用XCOM串口调试助手，其他串口软件可能控制DTR、RST导致MCU复位、程序不运行
 * 打开menuconfig菜单，找到Massive Storage Class选项，必须开启TinyUSB MSC feature 并且设置MSC FIFO size为4096(4K)
 * 
 * 编译踩坑记录：
 * 1. 编译报错 'CFG_TUD_CDC_EP_BUFSIZE' undeclared
 *    原因：新版 TinyUSB（esp_tinyusb）已将 CFG_TUD_CDC_EP_BUFSIZE 拆分为 CFG_TUD_CDC_TX_BUFSIZE 和 CFG_TUD_CDC_RX_BUFSIZE，
 *         但 managed_components/espressif__esp_tinyusb/usb_descriptors.c 中仍使用了旧宏名。
 *    解决：将 usb_descriptors.c 中第175行和第180行的 CFG_TUD_CDC_EP_BUFSIZE 替换为 CFG_TUD_CDC_TX_BUFSIZE。
 * 2. 运行时 sdmmc_card_init failed (0x107) 导致测试卡住
 *    原因：板子上没有插入 SD 卡时，sd_test() 函数会进入无限重试循环，需要手动按键才能跳过。
 *    解决：修改 main/APP/app_test.c 中的 sd_test() 函数，改为只尝试一次，失败后自动跳过（打印 "SKIP" 并继续下一个测试）。
 * 3. 进入 LVGL 界面时 sdmmc_card_init failed 反复打印
 *    原因：lvgl_demo() 在 images_init() 失败后进入 while(sd_spi_init()) 无限重试循环；
 *         app_ui.c 的 lv_rtc_timer 定时器（500ms周期）在 sd_check_en==0 时反复调用 sd_spi_init()。
 *    解决：lvgl_demo.c 改为单次尝试，无 SD 卡时跳过图片库更新直接进入 LVGL；
 *         app_ui.c 增加重试计数器，每 5 秒检测一次热插拔，避免每秒 2 次刷屏报错。
 * 
 ***********************************************************************************************************
 * 公司名称：广州市星翼电子科技有限公司（正点原子）
 * 电话号码：020-38271790
 * 传真号码：020-36773971
 * 公司网址：www.alientek.com
 * 购买地址：zhengdianyuanzi.tmall.com
 * 技术论坛：http://www.openedv.com/forum.php
 * 最新资料：www.openedv.com/docs/index.html
 *
 * 在线视频：www.yuanzige.com
 * B 站视频：space.bilibili.com/394620890
 * 公 众 号：mp.weixin.qq.com/s/y--mG3qQT8gop0VRuER9bw
 * 抖    音：douyin.com/user/MS4wLjABAAAAi5E95JUBpqsW5kgMEaagtIITIl15hAJvMO8vQMV1tT6PEsw-V5HbkNLlLMkFf1Bd
 ***********************************************************************************************************
 */