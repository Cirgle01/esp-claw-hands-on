#  从源码编译与烧录
参考链接：[text](https://esp-claw.com/zh-cn/reference-project/build-from-source/)
## 1 安装ESP-IDF环境
参考链接：[官网：在 Windows 上安装 ESP-IDF 及工具链](https://docs.espressif.com/projects/esp-idf/zh_CN/v6.0/esp32s3/get-started/windows-setup.html)
### 第一步：安装依赖包
依赖git,python(>=3.10)，参考上面的链接
### 第二步：安装 EIM
[官网下载链接](https://dl.espressif.cn/dl/eim/)

[安装文件直链](https://dl.espressif.cn/github_assets/espressif/idf-im-ui/releases/download/v0.12.6/eim-gui-windows-x64.msi)

### 第三步：下载ESP-IDF v5.5.4官方离线包
由于使用EIM在线下载会因网络问题导致子模块下载失败，因此使用官方离线包

[官网下载链接](https://dl.espressif.cn/dl/eim/index.html?tab=offline)

[文件直链](https://dl.espressif.cn/dl/eim/archive_v5.5.4_windows-x64.zst)

使用的是`.zst`格式的离线包，有其他官方链接提供`.exe`格式的离线包，不确定能不能用

### 第四步：使用 EIM 安装 ESP-IDF
EIM -> 新安装 -> 离线安装 -> 选中上一步下载的离线包

注：如果安装失败，检查有没有之前安装的其他版本没有完全删除

## 2 获取源码
``` sh
# 这里给出的是本仓库源码
git clone https://github.com/Cirgle01/esp-claw-hands-on.git
# 或者通过ssh方式clone（可能需要配置ssh）
# git clone git@github.com:Cirgle01/esp-claw-hands-on.git
cd esp-claw-hands-on/application/edge_agent
```
## 3 选择开发板
使用IDF终端（桌面的IDF_v5.5.4_Powershell或者EMI版本管理内打开）

注：此后所有`idf.py *`命令都要在IDF终端操作
``` sh
# 先安装esp-bmgr-assist
pip install esp-bmgr-assist
idf.py gen-bmgr-config -c ./boards -b esp32_S3_DevKitC_1_breadboard
```

## 4 调整 menuconfig 配置（可选）
``` sh
idf.py menuconfig
```

## 5 编译与烧录
``` sh
idf.py build
# 记得接板子
idf.py flash monitor
```
注：LLM api、WiFi信息等配置存储在ESP32 芯片的 NVS 分区，重新烧录不会丢失；但记忆存储在FATFS，重新烧录会丢失。猜测可能的解决办法：通过修改application\edge_agent\fatfs_image\memory恢复；添加新的skill持久化新增配置

# 硬件情况

## 核心板信息
以下是使用的鹿小班ESP32-S3-N16R8核心板的商家介绍内容:
```
ESP32-S3关键注意事项：
1. 引脚限制：GPIO34-GPIO37为板载Flash/PSRAM专用引脚，禁止用作普通IO
2. ADC限制：ADC2通道在WiFi开启时不可用，优先使用ADC1(GPIO0-GPIO7, GPIO16-GPIO21)
3. Strapping引脚：GPIO0,GPIO45,GPIO46为启动配置引脚，GPIO0拉低进入下载模式
4. USB-OTG:GPIO47/GPIO48为原生USB 引脚，可直接做USB主机/设备，无需额外芯片
5. PSRAM 支持：N16R8 自带8MB PSRAIM，工具菜单PSRAM选择OPI PSRAM即可启用

GPIO48:板载WS2812B RGB彩灯
```
> 注: 事实上, esp-claw使用了io15作为LCD连接线, 且没有出现错误

## 连接硬件信息
### 确认连接的硬件
1. RGB灯带(16颗灯珠)
2. LCD屏幕
3. DHT11 温湿度传感器
4. INMP441 麦克风
5. 板载WS2812B RGB彩灯

> 注: 其中屏幕和麦克风使用I2S配置, 对agent透明, Agent 通过 board_manager + audio/display Lua 模块使用即可

### GPIO对应表
| GPIO | 用途 |
|---|---|
| 1 | INMP441 SD（数据）
| 2 | INMP441 WS（字选）
| 4 | SPI SCLK（LCD）
| 5 | SPI MOSI（LCD）
| 6 | LCD RST
| 7 | LCD DC
| 13 | RGB灯带(16颗灯珠)
| 15 | LCD CS
| 16 | LCD 背光
| 17 | DHT11 温湿度传感器
| 42 | INMP441 SCK（位时钟）
| 48 | 板载WS2812B RGB彩灯

### 定义文件中设定内容
| GPIO | 用途 |
|---|---|
| 9 |PDM DAC 输出 (fake_audio_dac依赖,用于支持官方演示中三合一模块)
| 38 |RMT（WS2812 LED）(可能是官方板载led)
