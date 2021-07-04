{%- assign device = site.data.devices[page.device] -%}

## 解锁 Bootloader

{% include alerts/note.html content="设备只需执行一次解锁步骤。" %}
{% include alerts/warning.html content="解锁 Bootloader 可能会清空你设备上的所有数据，在这之前请确保你的重要数据已经备份。注意！OEM 厂商的备份文件可能无法在 exTHmUI 中被恢复。" %}

1. 参考[此教程]({{ device.unlock_bootloader_guide }})解锁 Bootloader。

{% include templates/recovery_install_fastboot_generic.md %}
