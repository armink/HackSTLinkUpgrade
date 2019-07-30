# 暴力升级你的 ST-Link

## 背景

一些 ST-Link 在使用最新的 IDE 时，经常提示需要升级其固件，但是升级始终失败，提示容量不足。

在 Keil MDK 上可能就提示一下升级失败，但仍然可以继续下载调试。可是当使用 ST 最新推出的 CubeIDE 时，情况就非常糟糕，你如果不升级成功，就没法让你继续使用，仿佛陷入了死循环，导致一些开发板完全无法使用 CubeIDE。

## 问题原因

这些开发板包括 RT-Thread 和 正点原子 联合推出的 [潘多拉 IoT 开发板](https://item.taobao.com/item.htm?spm=a230r.1.14.4.381759c10S57Js&id=583527145598&ns=1&abbucket=9#detail) 。[![iot_board](docs/images/iot_board.png)](https://item.taobao.com/item.htm?spm=a230r.1.14.4.381759c10S57Js&id=583527145598&ns=1&abbucket=9#detail)

该开发板上的 ST-Link 用的是 **STM32F103C8T6** ，C8T6 只有 64KB flash，在早期 ST-Link 固件比较小的时候，64KB 完全是够用的。但随着 ST-Link 的功能升级后，固件大小正好超过了 64KB ，导致了现在提示的升级错误，如下图所示。提示信息为：`The up-to-date firmware is too big for this board (4960 bytes in excess). Can't update `。就差这么 4K 多的空间了。

![upgrade_error](docs/images/upgrade_error.png)

## 解决思路

### 方案1：更换主控

最彻底的解决方法当然是更换 ST-Link 用的 MCU ，比如更换为 pip to pin 兼容的 CBT6（128KB  Flash） ，价格也没有差多少，但需要采购，手工拆卸下来，再焊接上一片新的，焊接完成后还得折腾一番才能烧入 ST-Link 固件，有些麻烦。大家如果有精力可以折腾下，一劳永逸。

### 方案2：修改 ST-Link 升级器软件

这里要首先讲一个常识，一般的 STM32 芯片片内都会在末尾预留一部分 Flash 空间出来，只是这部分 flash 空间 ST 不保证 Flash 质量。如果能将末尾预留 Flash 利用起来，`C8T6` 也许也能当 `CBT6` 来用。所以问题的重点就聚焦在如何让升级器软件 **取消 Flash 容量检查的限制**。

搜索一番，果真就有。这个方案出自这位老外：https://lujji.github.io/blog/installing-blackmagic-via-stlink-bootloader/ 。思路还是挺新颖的，大家如果有精力可以深入看一下。

虽然是两年前的方法了，但是也适用于笔者用的 STLinkUpgrade V3.3.0 。

## 如何使用

### STEP1：安装 Java 运行环境

如果电脑上没有 Java 运行环境，可以看这里：https://jingyan.baidu.com/article/4e5b3e1909043f91911e2464.html

### STEP2 ：双击打开 STLinkUpgradeHacked.jar

![step2](docs/images/step2.png)

### STEP3: 进入升级模式

![step3](docs/images/step3.png)

### STEP4：开始升级

点击 `Upgrade` 即可。

## 致谢

- 感谢大法师 [@lymzzyh](https://github.com/lymzzyh) 及 小白 [@Zero-Free](https://github.com/Zero-Free) 的支持，在他们的帮助下完成了 ST-Link 固件更换测试工作
- 感谢 [@lujji](https://github.com/lujji) 这位老外的修改教程，大家有兴趣也可以去他的 GItHub 逛逛 https://github.com/lujji

-----

## 注意

- **升级需谨慎**。虽然大多数情况下 预留的 Flash 继续使用没问题，但谁也不能完全保证可靠；
- 请勿用于商业用途；

