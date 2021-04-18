---
title: 安装win10双系统
date: 2019-10-03 11:11:02
tags: win10
---

## Dual System Boot win10 and MacOS

# 一. 背景

- 在安装完MacOS之后，发现不能上Google试了n种方式都失败告终，并且mac下载文件速度很慢、占用太多内存
  <!--more-->
- 因为z7-kp7gz电脑一到手就只安装了MacOS10.14.6 mojave，现在只需按照正常流程安装win10 企业版1809到机械硬盘上即可。

* 先安装macOS后安装win10的好处就是不需要自己去重建uefi引导和创建200M以上的esp分区

# 二. 准备

* [msdn I tell you](https://msdn.itellyou.cn) 下载[win10企业版系统](ed2k://|file|cn_windows_10_enterprise_ltsc_2019_x64_dvd_9c09ff24.iso|4478906368|E7C526499308841A4A6D116C857DB669|/)，注意`intel cpu 6`代以后不支持win7了
* 8G以上U盘一个
* [**Universal-USB-Installer-1.9.8.9.exe** ](https://www.pendrivelinux.com/universal-usb-installer-easy-as-1-2-3/)请在win环境下格式化成fat32制作启动盘，否则会无法识别
* **注意：** 西数固态sn750安装系统前使用`DiskGenius`格式化成GPT(GUID格式），否则会识别不出nvme的ssd

# 三. 安装步骤

* 按**F2** 进入bios或者**F7**引导项，选择win10安装u盘进入
* ![Jietu20191003-115130安装引导0.jpg](https://i.loli.net/2019/10/03/adyUKQw6WrCJnXq.jpg)
* 格式化机械硬盘的C盘，并点击一下步
* ![Jietu20191003-114902安装引导.jpg](https://i.loli.net/2019/10/03/Xt8G1yuhC29LWlx.jpg)
* 依次安装即可，重启后选择从硬盘启动，也可以拔出u盘
* [win10激活工具HWIDGen v52.01](https://github.com/codewindy/soft-common/blob/master/HWIDGen%2030.10.rar)下载和[相应的驱动](http://www.hasee.com/Chinese/drivers/drivers/index.php/Download/Index/model.html?id=525)
* 直接Win键+X，打开Windows Powershell输入以下命令
```shell
  slmgr /skms kms.03k.org
  slmgr /ato
  最后可以cmd命令输入”slmgr.vbs -dlv”来查看windows10激活状态。
```
* 最终效果如下
![151570158264_.pic_hd.jpg](https://i.loli.net/2019/10/04/W59GQXP2SuvmEx1.png)
* sn750 固态测速如下，感觉挺快的，质量一般
![IMG_7039.PNG](https://i.loli.net/2019/11/20/AKyIxW2hPLsV6lX.png)
# 四. 问题解决

* 记得安装所有驱动，包括游戏管理中心的驱动，会在任务管理器的性能选项中显示2个独立显卡

* 安装双系统之后重启进入win10会出现[时间差8小时](https://imac.hk/mac-os-x-windows-systme-time.html)
* win+x 进入管理员cmd模式执行代码

```shell
Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1/
```

* ![IMG_6635.PNG](https://i.loli.net/2019/10/03/P81dwv63MJDqzam.png)

# 五. 参考
* [永久激活win10企业版](https://www.isharepc.com/2137.html)
* [msdn.itellyou.cn](ed2k://|file|cn_windows_10_enterprise_ltsc_2019_x64_dvd_9c09ff24.iso|4478906368|E7C526499308841A4A6D116C857DB669|/) Windows 10 Enterprise LTSC 2019 (x64) - DVD (Chinese-Simplified) 系统下载
* [电脑店pe制作工具](https://u.diannaodian.com/)