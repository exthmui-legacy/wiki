{%- assign device = site.data.devices[page.device] -%}

## 解锁 Bootloader

{% include alerts/note.html content="设备只需执行一次解锁步骤。" %}
{% include alerts/warning.html content="解锁 Bootloader 可能会清空你设备上的所有数据，在这之前请确保你的重要数据已经备份。注意！OEM 厂商的备份文件可能无法在 exTHmUI 中被恢复。" %}

{% if device.project_spectrum_recovery %}
1. 下载 [Project Spectrum recovery]({{ device.project_spectrum_recovery }}).
{% endif %}
{% if device.project_spectrum %}
2. 下载 [Project Spectrum]({{ device.project_spectrum }}).
{% endif %}
{% if device.required_bootloader_link %}
3. 在[这里下载]({{ device.required_bootloader_link }})可解锁的 Bootloader。
{% endif %}
4. 将下载好的 .zip 放到 `/sdcard`:
    * 使用 adb 运行: `adb push filename.zip /sdcard/`
    * 你可以使用任何你觉得合适的方法。`adb` 在所有 Android 设备上都是可用的，可以在 Android 和 Recovery 下工作。
5. 如果你没有进入 Recovery，重启进入 Recovery:
    * {{ device.recovery_boot }}
{% if device.project_spectrum_recovery %}
6. 刷入 Project Spectrum recovery 然后再次重启进入 Recovery:
    * {{ device.recovery_boot }}
{% endif %}
{% if device.project_spectrum %}
7. 刷入 Project Spectrum zip.
{% endif %}
{% if device.required_bootloader_link %}
8. 刷入可解锁 Bootloader 的安装包
{% endif %}
9. 重启进入系统

{% unless device.no_oem_unlock_switch %}
10. 在开发者选项中打开 OEM 解锁
{% endunless %}
11. 使用 USB 连接你的设备到计算机
12. 在电脑上打开命令提示符 (Windows) 或终端 (Linux 或 macOS) 然后运行:
```
adb reboot bootloader
```
    {% if device.download_boot %}
    你也可以通过组合键进入 fastboot:

    * {{ device.download_boot }}
    {% endif %}
13. 当设备进入 fastboot 后, 输入此命令来检查电脑是否已经识别到设备:
```
fastboot devices
```
    {% include alerts/tip.html content="如果你在 Linux 或 macOS 上看到 `no permissions fastboot` 请尝试使用 Root 用户运行 `fastboot`。" %}
14. 现在输入这串命令来解锁 Bootloader:

{% if device.custom_unlock_cmd %}
    ```
{{ device.custom_unlock_cmd }}
    ```
{% else %}
    ```
fastboot oem unlock
    ```
{% endif %}

15. 如果设备没有自动重启，请手动。现在 Bootloader 应该解锁了。
16. 由于设备完全重置，您将需要重新启用 USB 调试以继续。

{% if device.before_recovery_install %}
{% capture path %}templates/device_specific/{{ device.before_recovery_install }}.md{% endcapture %}
{% include {{ path }} %}
{% endif %}

{% include templates/recovery_install_fastboot_generic.md %}
