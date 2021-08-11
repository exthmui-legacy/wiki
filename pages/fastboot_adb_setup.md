---
sidebar: home_sidebar
title: 使用 ADB 和 fastboot
permalink: adb_fastboot_guide.html
---
##  `ADB`是什么?
[Android调试桥](https://en.wikipedia.org/wiki/Android_Debug_Bridge) (`ADB`) 是一个便于Android设备和个人计算机之间的通信的开发工具。 这种通信通常通过 USB 线完成，但也支持 Wi-Fi 连接。

`adb` 就像是 Android 开发的一把“瑞士军刀”。 它提供了许多由命令 adb --help 详细描述的功能。 下面的"[常用的 ADB命令](#常用的 ADB命令)" 部分列出了一些更常用的命令。

## 安装 `adb` 和 `fastboot`

Google 托管了仅包括 `adb` 和 `fastboot`的zip文件。 您可以按照以下说明进行设置来使用。

### 在Windows上安装
1. 从Google下载[Windows zip](https://dl.google.com/android/repository/platform-tools-latest-windows.zip)。
2. 解压到某处，例如`%USERPROFILE%\adb-fastboot`
3. Windows 7/8:
    * 在桌面右键“计算机“ 再右键“属性”
    * 在查看计算机信息窗口中单击“高级系统设置”
    * 在“高级”选项卡中，单击“环境变量”按钮
    * 在“环境变量”窗口中，在“系统变量”区域选中变量“Path”后单击“编辑”按钮。
    * 在已有的“Path”定义后加上 `;%USERPROFILE%\adb-fastboot\platform-tools`  （每个变量之间用分号隔开）
4. Windows 10:
    * 搜索“高级系统设置”
    * 选择"查看高级系统设置"
    * 单击“高级”选项卡
    * 单击“环境变量”按钮，打开“环境变量”窗口
    * 选择“系统变量”下的“Path”变量后单击“编辑”按钮。
    * 单击“新建”按钮
    * 插入% `%USERPROFILE%\adb-fastboot\platform-tools` in the text field
5. 安装 [通用ADB驱动](https://github.com/koush/UniversalAdbDriver)然后重启

### 在macOS上安装
1. 从Google下载[macOS zip](https://dl.google.com/android/repository/platform-tools-latest-darwin.zip)。
2. 解压到某处，例如 `~/adb-fastboot`。
3. 将如下内容添加到`~/.bash_profile`:
```sh
if [ -d "$HOME/adb-fastboot/platform-tools" ] ; then
    export PATH="$HOME/adb-fastboot/platform-tools:$PATH"
fi
```
4. Log out and back in.（译者没有Mac，请Mac用户指出正确翻译）

### 在Linux上安装
1. 从Google下载[Linux zip](https://dl.google.com/android/repository/platform-tools-latest-linux.zip)。
2. 解压到某处，例如 `~/adb-fastboot`。
3. 将如下内容添加到 `~/.profile`:
```sh
if [ -d "$HOME/adb-fastboot/platform-tools" ] ; then
    export PATH="$HOME/adb-fastboot/platform-tools:$PATH"
fi
```
4. 注销后重新登录
5. 你可能也需要设置udev规则查看[这个仓库](https://github.com/M0Rf30/android-udev-rules#installation)获取更多信息

## 设置`adb`

要在你的设备上使用`adb` ，你需要打开开发者选项和USB调试（备注：此过程限于Android4.2及更高，exTHmUI最低为Android9，故不写更老版本的操作方法）

1. 打开设置，选择“关于”
2. 点击七次版本号
3. 返回，然后点击开发者选项
4. 下滑，打开“调试”下的“Android调试”或者“USB调试”
5. 将你的设备插上电脑
6. 在电脑上打开终端/命令行窗口后输入`adb devices`.
7. 一个对话框应该会出现在你的设备上，询问你是否允许USB调试，勾选“始终允许”再选择“是”

好！`ADB`已经可用于你的设备

## 设置 `fastboot`

使用 `fastboot` （如果你的设备支持的话）应该重启到fastboot模式。在 fastboot 模式下，你可以输入 `fastboot devices` 来确保你的设备被检测到。

## 常用的 `ADB`命令

* `adb shell` - 在设备上启动一个[shell](https://en.wikipedia.org/wiki/Shell_(computing)) 
* `adb push <local> <remote>` - 将文件`<local>` 推送到 `<remote>`
* `adb pull <remote> [<local>]` - 将文件 `<remote>` 拉取到 `<local>`。若`<local>` 未被指定，`<remote>`将会被拉取到当前文件夹
* `adb logcat` - 允许你实时查看设备的日志。你可以使用`adb logcat -b radio`来查看 radio 日志，用 `adb logcat -C` 以彩色模式查看日志
* `adb install <file>` - 在你的设备上安装所给的.apk文件
