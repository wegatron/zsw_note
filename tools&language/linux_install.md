# Linux安装踩坑
自以为安装个系统再简单不过了, 制作完启动盘, 分好区, 一路next半小时就完了. 没想到这次折腾了一天, 触及到了很多认知的盲点, 这里做一些小记.


## ios 刻录
建议使用: https://www.balena.io/etcher/ 开源+带刻录校验+支持多种操作系统

## 准备工作&新的认知
* Linux版本选择
    无脑选ubuntu, 支持最好(源最全)的一个系统. 但ubuntu的UI实在太丑, 用起来也不方便. 很久之前接触了mint(ubuntu的改良版本, cinnamon桌面), 这个UI好用很多, 且完全支持ubuntu的源.

* 启动盘的制作
    ①utriso 这个工具使用很久了, 但是现在使用EFI作为引导之后, 这个工具就不是很好用了. usb hdd这些格式无法正常引导, 使用raw格式的话使用完毕之后要还原U盘也很麻烦.
    
    ②[rufus](https://rufus.ie/), 用该工具刻录了mint 19.3的iso, 均无法正常引导.
    
    ✔️③推荐使用mint自带的usb image writer, 这个[教程](https://linuxmint-installation-guide.readthedocs.io/zh_CN/latest/burn.html)写得很好

* 系统引导([参考博文](https://blog.nanpuyue.com/2017/037.html))
    以前知道BIOS(Legacy BIOS)+MBR来引导系统启动. BIOS历史悠久, 数十年没有太大的变化, 已经不太适应计算机的发展, 如今, UEFI已经全面取代BIOS.
    > MBR 是 Master Boot Record 的缩写，又叫做主引导扇区，是计算机开机后访问硬盘时所必须要读取的首个扇区。在深入讨论主引导扇区内部结构的时候，有时也将其开头的446字节内容特指为“主引导记录”（MBR），其后是4个16字节的“磁盘分区表”（DPT），以及2字节的结束标志（0x55AA）。因此，在使用“主引导记录”（MBR）这个术语的时候，需要根据具体情况判断其到底是指整个主引导扇区，还是主引导扇区的前446字节。

    UEFI(Unified Extensible Firmware Interface), 它突破了 MBR 分区表4个主分区及最大 2.2T 容量的限制, 并有如下优点:
    * 支持文件系统，可以直接读取分区上的文件
    * 可以存储启动管理器的路径并直接引导之
    * 可以加载并运行 EFI 应用
    * 提供 EFI Shell
    
    UEFI 的引导依赖于分区表上的一个特殊分区，叫 EFI 系统分区（EFI System Partition），UEFI 标准中规定 EFI 系统分区为 FAT32, 同时支持 FAT12/FAT16 作为移动介质的文件系统.

## Linux安装的一些坑
这里记录一些在我的Dell G3 3779笔记本上安装ubuntu, mint的一些坑.
1. Secure Boot
    UEFI 提供了安全启动（Secure Boot）的功能，安全启动功能启用时, UEFI 会验证启动管理器的的合法性，一般来说验证是通过数字签名来实现的.
    第一次安装时, 下载了mint 19.3, 在勾选安装第三方库(media library等)时有一个提示:
    >“Actually, Installing any third-party software with Secure Boot requires registering new Machine owner key(MOK) into EFI. Any Linux installer registers a new MOK variable before it finishes the installation. If due to an error Installer is interrupted and Installation is not finished completely, you may get this error.”

    由于没发现我的SSD(第2个坑), 因此中断了安装. 此后, 启动盘就进不去了. 错误和[此篇博文](https://smarttechnicalworld.com/cant-install-ubuntu-efibootmmx64-efi-not-found/)一模一样. 参考博文想删掉MOK参数, 却报找不到这个variable, 估计我这里还没开始安装, 因此这个variable也没添加进去(不知道什么原因).

    ✔️解决办法: 在Dell的BIOS设置中可以手动添加引导efi文件. 不过后来mint19.1和ubuntu18.04都不需要此操作即可正常引导进入(不知道为什么).
2. NVME SSD
✔️Dell 默认是使用RAID的磁盘模式, linux只有在ACHI下才能识别到NVME的盘, 而且ACHI模式貌似访问速度更快, bug更少. 改完之后, 第一次引导会跳出内存检测的页面(真心bug, 还以为失败了).
[一些关于ACHI, RAID, NVME的讨论](https://www.v2ex.com/t/534791)

3. 关于分区
    以前使用MBR引导时, 安装linux只需要分自己要用到的分区就行了, 我的习惯是: `/`, `/home`, `swap`.
    ✔️但是, 现在使用UEFI之后, 需要新建一个efi分区, 没有这个分区, 系统就无法正常引导.

4. Dell 笔记本linux兼容性
    Dell笔记本不对linux的兼容性也是一个坑, Dell G3 3779不兼容<=4.18的linux kernel. 而linux mint19.1的kernel版本为4.15(系统无法正常关机和重启). 亲测mint19.3的kernel为5.0, 但更新之后, 进入系统就只显示黑屏幕和鼠标. [关于此的讨论](https://www.dell.com/community/Linux-General/G3-17-3779-Ubuntu-18-04-LTS/td-p/6106352)
    
    ✔️解决办法: ubuntu 18.04(kernel 5.3)可以正常安装使用.

## linux 挂在 windows分区无法写入的问题
> This issue is caused by the Windows fast startup (also called hybrid boot or hybrid shutdown) option, which is enabled by default. When shutting down Windows, it hibernates, allowing for a faster boot. This is a nice option to have on Windows, but it causes the problem I mentioned above when dual booting Windows and Linux.

> You can disable fast startup in Windows 10 or 8 this by launching the Control Panel, search for Power Options and click it. Next, click on the Choose what the power buttons do item from the left-hand sidebar.

[Fix Windows 10 Or 8 Partition Mounted As Read-Only On Linux When Dual Booting](https://www.linuxuprising.com/2019/01/fix-windows-10-or-8-partition-mounted.html)

## 基本软件安装

```bash
sudo apt install -y cmake synaptic git emacs openssh-server filezilla fcitx foxit-reader
sudo dpkg -i google-chrome-stable_current_amd64.deb wps-office_11.1.0.9505_amd64.deb 
tar -jxvf Zotero-5.0.85_linux-x86_64.tar.bz2 -C ~/opt/
tar -zxvf pycharm-community-2020.1.tar.gz  -C ~/opt/
chmod 400 id_rsa
cp id_rsa ~/.ssh
ssh-add
```

* deepwine 安装
    参考 https://github.com/zq1997/deepin-wine
    中文乱码问题: 安装fonts-wqy-microhei完美解决

    对于英文系统, 在启动脚本中增加
    ```
    export LANG=zh_CN.UTF-8
    export LANGUAGE=zh_CN:en_US
    ```

* sougou
    `CTRL` + `;` 默认为选择粘贴历史. 可以通过系统的 `Fcitx Configuration`-> `Add on` -> `clipboard fcitx access`进行去除


## linux 删除多余的kernel

```bash
#显示当前分区
uname -r
```

再在包管理器中搜索`linux-image`, `linux-headers`查找相应的没有用的kernel(按照安装状态排序), 删除无用的kernel.

自动清理, 参考: https://softhints.com/linux-mint-volume-boot-disk-space-remaining/
```bash
df -h /boot # 查看boot的使用情况

sudo du -sh /boot/* # 查看boot下文件列表以及其所占用的空间大小

sudo apt-get autoclean && sudo apt-get autoremove # 自动清理不需要的软件包
# apt-get autoclean 清理过时的dep-packages
# apt-get autoremove 清理独立的package, 不被其他地方引用到的package
```

## fix broken package

```bash
sudo apt --fix-broken install
```

## 显卡驱动问题
进入recovery-mode, enable network, `apt install nvidia-driver-[515]`

在更新显卡驱动失败的时候, 可以尝试在命令行安装, 此时会显示依赖失败的问题, 从而逐个解决.


## Reference
[linux 安装教程](https://linuxmint-installation-guide.readthedocs.io/zh_CN/latest/burn.html)
[ACHI-RAID-NVME](https://www.v2ex.com/t/534791)
[BIOS UEFI介绍](https://blog.nanpuyue.com/2017/037.html)
