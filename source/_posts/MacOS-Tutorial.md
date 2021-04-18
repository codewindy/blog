---

title: 黑苹果安装教程
date: 2019-09-25 22:06:10
tags: mac
---
MacOS Install Tutorial
---
# 一. 背景

* 原先准备入手**thinkpad x1 extreme gen2**或**MacBook pro**，都需要额外买扩展坞迫于预算有限于是默默寻找黑苹果的适配机型**z7-kp7gz**
  <!--more-->
* 硬件信息如下 总共花了 **6249.9** RMB
```javascript
  | Component | Model                                                        |
  | --------- | ------------------------------------------------------------ |
  | CPU       | Intel Core(TM) i7-8750H                                      |
  | GPU       | Intel UHD630 / Nvidia GTX1060 (Z7m 为 1050Ti；皆无法驱动)       |
  | RAM       | Micron Crucial 8GB+16GB                                       |
  | NVMe      | WD Black SN750 NVME SSD 500G                                  |
  | SSD       | Phison SATA SSD 128G + 1T 机械硬盘                             |
  | Wireless  | Intel AC9462/9560（无法驱动，已更换 BCM94360CS2）                |
  | Ethernet  | Realtek RTL8168H                                             |
  | Audio     | Realtek ALC269vc                                             |

  # What's working

  - 睿频、变频正常（使用 18 款 MacBook Pro SMBIOS，最低 800Mhz, 最高睿频 4.1GHz）
  - Intel UHD630（已应用显存补丁，2048 MB）
  - 亮度调节（可在设置中调节或使用 Fn+F11, Fn+F12 快捷键）
  - I2C HID 触控板（需要正确驱动电池后才可以设置手势）
  - 有线网卡
  - 声音（ALC269vc, 使用 AppleALC 仿冒，注入 layout-id 为 88，外放、耳机、麦克风全部正常）
  - 电池状态（现已使用 Clover Hotpatch 驱动）
  - USB （使用 USBInjectAll + SSDT 驱动，3.0 5G/s 速度正常，Type-C 可用）
  - 睡眠（使用 Clover Hotpatch 修复）
  - etc.

  注：以上情况基于 KP7GZ 系列机型；对于使用其它机型的用户，可能会有一些功能不正常，请用户测试后反馈。

  # What's partial working

  - 蓝牙 (需要热启动 macOS 才可用，即先进入 Winodws 后重启进入 macOS、或者在 macOS 下使用虚拟机模拟热启动过程，不支持 AirDrop.)

  # What's not working

  - 独立显卡（GTX1060， 目前没有适用于 Mojave 的 Nvidia WebDriver）
  - 无线网卡（Intel AC9462 无解，使用蓝牙共享网络、USB共享网络或者USB网卡替代，或更换无线网卡）
  - **HDMI/MiniDP（该模具 HDMI/MiniDP 直接由独显输出, 独显无法驱动，所以 HDMI/MiniDP 也无法使用）**
  - 读卡器（读卡器走的 USB 2.0 外置，无法使用）

  注：请注意，目前已知的**所有**同方模具神舟战神机型（包括但不限于本 EFI 支持的所有机型），自带的 Nvidia 显卡 (无论是 1050Ti, 1060 或所有 GTX 16/RTX 20 系显卡) ，在 10.13.6 版本下**皆无法被正常识别并驱动**。目前针对此问题没有任何解决方案。我们建议不要把大好青春浪费在研究驱动独显上。关于外接显示器的解决方案，请参见硬件兼容性报告。

  **除非 Nvidia 官方更新 10.14 的 WebDriver，否则独显、HDMI 不可用的问题将无法解决，请避免再提类似的 issues.**
```
# 二. 准备条件

* 一个8G以上 u盘
* 下载刻录苹果系统MacOS到u盘的软件 [etcher](https://www.balena.io/etcher/)
* 下载[黑果小兵](https://blog.daliansky.net/macOS-Mojave-10.14.6-18G87-Release-version-with-Clover-5033-original-image.html)制作好的Mac系统 [macos_mojave_10.14.6_clover5033](https://mirrors.dtops.cc/iso/MacOS/daliansky_macos/)  ,下载完后注意校验文件的md5值一致,保证系统文件完整.
* Z7-kp7gz [笔记本电脑对应的EFI](https://github.com/kirainmoe/hasee-tongfang-macos)
* bcm94360cs2 NGFF转接卡 请自己淘宝

# 三.安装过程

* <font style="color:black; background:yellow">注意安装过程中不需要安装多次，即第一次从u盘启动，格式化硬盘后后面都是boot from mac启动</font>
* 将电脑 D面的11个螺丝取下，断开内置电源排线，替换内置的无线网卡Intel AC9560为淘宝165元买的白苹果`bcm94360cs2`，注意安装NGFF转接卡的时候固定螺丝用电胶带绝缘处理，黑白色的天线也需要替换，向上拔就行
* ![IMG_6578.JPG](https://i.loli.net/2019/09/24/A1YPOstH7yBRUCN.jpg)
* ![51569325210_.pic_hd copy.jpg](https://i.loli.net/2019/09/25/XIdeuJBRcy4pjri.jpg)
* 注意首先按F2 进入bios将安全启动Secure Boot 改成 Disable
* 然后插入u盘 按F7 从u盘启动开始进入安装引导
* 点击磁盘工具，格式化需要安装的硬盘为apfs格式 GUID分区图
* ![14.png](https://i.loli.net/2019/09/24/WBDbdaNM596Ei1o.png)
* 后面的步骤一直点击下一步即可直到进入macos系统
* 联网下载[clover configuration](https://github.com/codewindy/mac_soft/blob/master/Clover%20Configurator.zip)然后mount EFI分区，并替换成[github下载的z7-kp7gz](https://github.com/kirainmoe/hasee-tongfang-macos/releases/download/latest/kp7gz.zip)
* ![Screen Shot 2019-09-25 at 9.57.17 AM.png](https://i.loli.net/2019/09/25/MunhEyB5LPKVsji.png)
* ![Screen Shot 2019-09-25 at 9.59.26 AM.png](https://i.loli.net/2019/09/25/Mn6pEXtsLx2wFcW.png)
* 执行以下优化shell脚本即可
```shell
  sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/kirainmoe/hasee-tongfang-macos/master/Addons/optimize.sh)"
 新脚本 curl -fsSL https://efi.kirainmoe.com/installer > inst && chmod +x ./inst && sudo ./inst
```

# 五. 效果

* 优化后的效果如下
* ![Xnip2019-11-23_13-34-53.jpg](https://i.loli.net/2019/12/16/NTlkZQPOjeUH8nB.jpg)
* ![Xnip2020-03-03_22-30-12_cpu](https://i.loli.net/2020/03/10/qoLNDKrF1BUXvjg.jpg)
# 六. 问题解决

* 常见：安装完成后重启发生内核 panic，卡在 `VTDecoderXPCService` 错误：请在 Clover 界面按 O，定位到显卡设置（带有 Graphics 字样），勾选 **inject-intel**，并修改 **-platform-id** 从 **0x12345678** 修改为 <font style="color:black; background:yellow">0x3E9B0000</font>
* ![IMG_1005.JPG](https://i.loli.net/2019/09/25/DicFNPE1T6hbzLx.jpg)
* 因重新安装win10到sn750 nvme固态硬盘上会在clover 引导启动项会多出冗余，直接进入u盘安装的PE系统打开 bootice 找到多余的启动项，我这里多余的是`Phison SATA SSD`里面的EFI文件，然后重启电脑进入macOS系统，直接打开`clover configurator`软件加载EFI，然后删除Microsoft文件夹后再次重启即可。
* ![IMG_9972.jpeg](https://i.loli.net/2019/11/20/EVbGyz3jLYalPcq.jpg)
* ![Xnip2019-11-21_22-43-14.jpg](https://i.loli.net/2019/11/21/Ot82967gcQGFPWJ.jpg)


# 七. 升级tips

* <font style="color:black; background:yellow">如果你是第一次替换完配置文件，或者你在系统内 OTA 更新了新版本的系统，那么请在终端中执行以下命令重建 kext 缓存，并重启电脑：</font>
  ```javascript
  sudo kextcache -i /
  ```
* 解决电脑睡眠后摄像头问题
 ```javascript
  sudo killall VDCAssistant
 ```
* 解决macOS安装外部软件的问题
 ```javascript
  sudo spctl --master-disable
  control + space 空格 切换系统自带输入法
  sudo scutil --set HostName  windys-MacBook-Pro 修改电脑host名称 跟linux系统修改不同
 ```
* 系统升级到Catalina10.15.1之后偶尔会卡死在GUI桌面上
  ```javascript
      sudo shutdown -h now  
      sudo reboot
  ```
* macOS系统vim语法和配置刷新dns
  ```javascript
      cp -r /usr/share/vim/vimrc ~/.vimrc
      echo "syntax on">>~/.vimrc
      sudo killall -HUP mDNSResponder;
      sudo killall mDNSResponderHelper;
      sudo dscacheutil -flushcache
  ```
* [正确开启HIDPI](https://hackintosh.kirainmoe.com/an-zhuang-hou/chang-yong-ming-ling-he-you-hua#zheng-que-kai-qi-hidpi)
  
```shell
  #注意必须使用 1424x802 的分辨率，才能在休眠唤醒的时候不遇到四分之一屏和花屏。
    #使用以下代码开启（来自小米 Pro 的仓库）：
    #修改分辨率可以直接在系统设置里面选择显示Display来勾选对应的尺寸
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/daliansky/XiaoMi-Pro-Hackintosh/master/one-key-hidpi/one-key-hidpi.sh)"

  #如要恢复，删除 #/System/Library/Displays/Contents/Resources/Overrides/DisplayVendorID-9e5 即可。如果你是 Catalina 以上系统，你可能需要进入 Recovery 模式下挂载系统分区操作
  cd /Volumes/"Your System Disk Part"/System/Library/Displays/Contents/Resources/Overrides
  rm -rf ./DisplayVendorID-9e5
  cp -r ./backup/* ./
```
* [在macOS系统下右键点击](https://zh.wikihow.com/%E5%9C%A8Mac%E4%B8%8A%E5%8F%B3%E9%94%AE%E7%82%B9%E5%87%BB) 
```shell
    1. 直接按住control 并点击trackpad 触摸板
    2. 通过在设置里面找到trackpad 进行设置point&click 找到Secondary click 选择自己喜欢的方式即可
```
* 解决 10.14 以上系统字体渲染模糊
  ```shell
  defaults write -g CGFontRenderingFontSmoothingDisabled -bool NO
  ```


# 八. 参考
* [黑果小兵](https://blog.daliansky.net/) 下载MacOS 和 tutorial
* [kirainmoe 战神kp7gz](https://github.com/kirainmoe/hasee-tongfang-macos)下载 EFI 和安装运到的问题
* [mac软件下载 国外](https://macdownload.org/) 
* [mac精品软件下载](https://macwk.com/)
* [精品mac应用分享](https://xclient.info/)
* [https://aneeo.com/ios ](https://aneeo.com/ios)
* [hasee kp7gz](http://en.haseecomputer.com/PC/detail_1078.html)
* [macAppDownload.com](https://www.macappdownload.com/cleanmymac-x/)
* [appsTorrent.ru](https://www.appstorrent.ru/programs/)
* [macwk](https://macwk.com/)
* [Clover Configurator](https://mackie100projects.altervista.org/)
