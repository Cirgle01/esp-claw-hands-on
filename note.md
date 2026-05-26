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
记得接板子
``` sh
idf.py build
idf.py flash monitor
```
