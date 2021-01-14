# Hackintosh-OptiPlex-7080-MT

## 简介
Dell OptiPlex 7080 系列EFI， 支持Catalina或更高版本，因存在IRQ冲突导致声卡无法驱动，已屏蔽HPET

## 硬件
* **CPU**: Intel® Core™ i5-10500
* **IGPU**: Intel® UHD Graphics 630
* **RAM**: 8GB DDR4 2666 Daul Channel
* **SSD**: APPLESSD & Samsung SSD 860 EVO 
* **Wi-Fi & Bluetooth**: BCM943602CDP

## 可用
* CPU 睿频
* IGPU 图形加速，HEVC H.264 En/Decode 
* ALC256 所有输入输出正常 LayoutID 67 线路输出独立，可选内置扬声器与耳机 或 线路输出与耳机自动切换(已向AppleALC PR等待下一版本Release合并)麦克风工作需要VerbStub和ComboJack
![演示.png](https://github.com/Dynamix1997/Hackintosh-Dell-OptiPlex-7080-Series/blob/main/Pic/演示.gif)
* 除最靠近网口的USB2.0(为仅保留15端口而屏蔽)其他的正常
* LAN & Wireless Network
* Airdrop & Airplay
* 睡眠唤醒

## 补充及说明
* USB端口定制方面本EFI使用空壳Kext 基于7080MT定制，SMBIOS iMac19,2
* 不同的网卡的安装方式可能蓝牙HCI位置可能不同，我的蓝牙USB跳线直接插在主机后的第二个USB2.0 Type-A 端口 (HS09) 
* 传感器方面，相关驱动请自行添加
* WhateverGreen 使用的精简版 仅包含Force Online 核心显卡，管理引擎的重命名以及注入 Metal Device Name (仅支持FakeID 3E9B) 有独立显卡的请自行更换
* IOKitPersonalitiesInjector 空壳驱动 包含iMac19，2 SMBIOS仅核显的AGPM注入来修复核显视频处理时满载满频且不保留基本图形渲染性能而导致UI卡顿的问题 USB定制 修改系统报告SATA控制器的名称 (GenericAHCI → Intel 14 Series Chipset) 仅作修饰， 以及USB电流属性注入(仅针对11.0 的新IOProviderClass ：AppleUSBHostResources，之前版本为AppleBusPowerController)
* 可为 iOS 设备提供12W的充电功率 (5v 2.4A)

## 不可用
* 数字音频已屏蔽 (若要启用删除 "No-Hda-gfx" 属性)
* 如果DP音频无法识别 且`IORegistryExplorer`中 HDEF 下只有一个 `IOHDACodecDevice` 请在Config.plist → DeviceProperties → PciRoot(0x0)/Pci(0x1F,0x3) 下添加 "device-id" Data 709D0000 并加入 `FakePCIID.kext` 和 `FakePCIID_Intel_HDMI_Audio.kext` 同时为防止Sleep Wake 后出现 AppleHDAHDMI_DPDriver SetPowerState Panic 打开 Config.plist → Kernel → Quirks → PowerTimeOutKernelPanic
* DP音频正常时 如下图所示
![Digital Audio Working.png](https://github.com/Dynamix1997/Hackintosh-Dell-OptiPlex-7080-Series/blob/main/Pic/HDA.jpg)

## 关于近期发现7080MT存在的问题
* 连接以太网缆线可能导致Darkwake(仅主机唤醒，显示器仍处于熄灭状态称为DarkWake)  再Auto Sleep 可能出现的电源键指示灯状态是睡眠💤(呼吸灯闪烁) 但是主机后的电源指示灯依旧亮着 说明系统已经睡眠但是电源未切换至待机状态 且电脑无法唤醒稍等片刻电脑断电
针对该问题其他机型是否存在未知，目前靠Disable RTC Wake Schedule Patch 防止Power Nap Wake 尽可能阻止Darkwake 行为防止此问题
* 也可以选择安装独立以太网卡解决

## BIOS 设置
* System Configuration → SATA Operation: ***AHCI***
* Secure Boot → Secure Boot Enable: ***Disabled***
* System Configuration →  SerialPort ***Disabled*** （建议)
* Secure → PTT Secure(TPM) ***Disabled*** （建议)
* Secure → Intel SGX ***Disabled*** （建议)
* System Configuration → PCI Slot ***Disabled*** (如果PCI插槽为空，仅TM系列)

## 修改DVMT和CFG LOCK
* 无法使用Grub Setup_var 需要用到Ru.efi 将Ru.efi在BIOS中添加进Boot Menus 后启动 进入Ru后按 "Alt" + "=" 并查找 **CPUSetup** 和 **SaSetup**
* 解锁"CFG-LOCK" 找到CPUSetup 将横排 "0030" "0E" 位改为 00 按 Ctrl + W 保存
![Unlock CFGLOCK.png](https://github.com/Dynamix1997/Hackintosh-Dell-OptiPlex-7080-Series/blob/main/Pic/CFG-LOCK.png)
* 修改DVMT 搜索 SaSetup 将横排 "00F0" "05" 位改为 "02" 按 Ctrl + W 保存
![Set 64MB DVMT.png](https://github.com/Dynamix1997/Hackintosh-Dell-OptiPlex-7080-Series/blob/main/Pic/DVMT.png)
