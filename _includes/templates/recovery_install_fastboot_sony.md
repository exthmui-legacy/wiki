{%- assign device = site.data.devices[page.device] -%}

## 解锁 Bootloader

{% include alerts/note.html content="设备只需执行一次解锁步骤。" %}
{% include alerts/warning.html content="解锁 Bootloader 可能会清空你设备上的所有数据，这还包括存储在Trim Area 分区 (也称为TA) 中的 DRM 密钥。在这之前请确保你的重要数据已经备份。注意！OEM 厂商的备份文件可能无法在 exTHmUI 中被恢复。如果你想备份 TA 分区，你可以在互联网上找到与您的设备相关的教程。" %}

{% if device.install_variant and device.install_variant contains "sony_unlock_contacts" %}
1. 打开 `联系人`
2. 点击右上方的菜单按钮。
3. 点击 `设置`.
4. 长按 (大约5秒) 导航栏上方的空白位置，然后松开。一个名为 `输入服务代码` 的选项将出现在相同的区域 (这可能需要几次尝试)。
5. 点击 `输入服务代码` 后，将出现一个拨号器。
{% endif %}

1. 在电话应用中输入 `*#*#7378423#*#*` (`*#*#SERVICE#*#*`) 启动服务菜单。
2. 前往 `service info` > `configuration` 然后检查 `rooting status` - 你只能在它显示 `Bootloader unlock allowed: Yes` 时继续下一步操作。
3. 使用 USB 连接你的设备到计算机
4. 在电脑上打开命令提示符 (Windows) 或终端 (Linux 或 macOS) 然后运行:
```
adb reboot bootloader
```
    {% if device.download_boot %}
    你也可以通过组合键进入 fastboot:

    * {{ device.download_boot }}
    {% endif %}
5. 当设备进入 fastboot 后, 输入此命令来检查电脑是否已经识别到设备:
```
fastboot devices
```
    {% include alerts/tip.html content="如果你在 Linux 或 macOS 上看到 `no permissions fastboot` 请尝试使用 Root 用户运行 `fastboot`。" %}
6. 参考 [Sony 官网](http://developer.sonymobile.com/unlockbootloader/unlock-yourboot-loader/)的说明来生成解锁码。
7. 使用解锁码来解锁 Bootloader:
```
fastboot oem unlock <your_unlock_code>
```
8. 由于设备完全重置，您将需要重新启用 USB 调试以继续。

{% if device.install_variant and device.install_variant contains "sony_init_fota" %}

{% if device.custom_recovery_codename %}
{% assign custom_recovery_codename = device.custom_recovery_codename %}
{% else %}
{% assign custom_recovery_codename = device.codename %}
{% endif %}

## 通过 `fastboot` 安装自定义 Recovery

{% if device.custom_recovery_link %}
1. 你可以在[这里下载]({{ device.custom_recovery_link }})自定义 Recovery。
{% else %}
{% if device.uses_twrp %}
1. 你可以在[这里下载](https://dl.twrp.me/{{ custom_recovery_codename }})最新版本的 TWRP，文件名应该看起来像这样 `twrp-x.x.x-x-{{ custom_recovery_codename }}.img`。
{% else %}
1. 你可以在[这里下载](https://download.exthmui.cn/exthmui/{{ custom_recovery_codename }}) exTHmUI Recovery。
{% endif %}
{% endif %}
2. 使用 USB 连接你的设备到计算机
3. 在电脑上打开命令提示符 (Windows) 或终端 (Linux 或 macOS) 然后运行:
```
adb reboot bootloader
```
    {% if device.download_boot %}
    你也可以通过组合键进入 fastboot:

    * {{ device.download_boot }}
    {% endif %}
4. 当设备进入 fastboot 后, 输入此命令来检查电脑是否已经识别到设备:
```
fastboot devices
```
    {% include alerts/tip.html content="如果你在 Linux 或 macOS 上看到 `no permissions fastboot` 请尝试使用 Root 用户运行 `fastboot`。" %}
5. 在设备上临时刷入一个 Recovery 镜像:
```
fastboot flash boot twrp-x.x.x-x-{{ custom_recovery_codename }}.img
```
    {% include alerts/tip.html content="该文件的名称可能与此命令中的名称不一致，可能需要进行调整。如果需要调整的话，记住也要在下面的命令中调整文件名。" %}
6. 重启到 TWRP:
```
fastboot reboot
```
7. 将 TWRP 镜像复制到设备:
```
adb push twrp-x.x.x-x-{{ custom_recovery_codename }}.img /sdcard
```
8. 运行以下命令:
```
adb shell
```
9. 将 TWRP 写入 `recovery` 分区:
```
dd if=/sdcard/twrp-x.x.x-x-{{ custom_recovery_codename }}.img of=/dev/block/platform/msm_sdcc.1/by-name/FOTAKernel
```
10. 运行以下命令:
```
exit
```
{% else %}

{% if device.is_ab_device and device.has_recovery_partition != true %}
{% include templates/recovery_install_fastboot_ab.md %}
{% else %}
{% include templates/recovery_install_fastboot_generic.md %}
{% endif %}

{% endif %}
