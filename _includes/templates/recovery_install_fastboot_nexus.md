{%- assign device = site.data.devices[page.device] -%}

## 解锁 Bootloader

{% include alerts/note.html content="如下步骤一个设备只需跑一次" %}
{% include alerts/warning.html content="解锁 Bootloader 将会清除设备上所有数据！
在继续之前，请确保您要保留的数据已备份到您的 PC 和/或您的 Google 帐户，或其他等效的地方。 请注意，一旦安装 exTHmUI， 三星和摩托罗拉备份等 OEM 的备份文件在exTHmUI上不可用。" %}

{% unless device.no_oem_unlock_switch %}
1. 在设备设置下的开发者选项中启用 OEM 解锁（如果有）。
{% endunless %}
2. 通过USB线把设备预电脑连接。
3. 在电脑上，打开一个命令提示符窗口 (Windows) 或终端窗口(Linux或macOS)，然后输入
```
adb reboot bootloader
```
    {% if device.download_boot %}
    你也可以通过如下按键组合进入fastboot模式：
    
    * {{ device.download_boot }}
    {% endif %}
4. 一旦设备处于fastboot模式，请确认您的电脑找到它：
```
fastboot devices
```
    {% include alerts/tip.html content="如果你在Linux或者macOS上看见`no permissions fastboot`，请用root模式执行" %}
5. 现在，输入如下命令解锁Bootloader

{% if device.custom_unlock_cmd %}
    ```
{{ device.custom_unlock_cmd }}
    ```
{% else %}
    ```
fastboot oem unlock
    ```
{% endif %}

6. 如果设备没有自动重启，手动重启。现在它应该被解锁了。
7. 由于设备完全重置，您将需要重新启用 USB 调试以继续。

{% if device.before_recovery_install %}
{% capture path %}templates/device_specific/{{ device.before_recovery_install }}.md{% endcapture %}
{% include {{ path }} %}
{% endif %}

{% if device.is_ab_device and device.has_recovery_partition != true %}
{% include templates/recovery_install_fastboot_ab.md %}
{% else %}
{% include templates/recovery_install_fastboot_generic.md %}
{% endif %}
