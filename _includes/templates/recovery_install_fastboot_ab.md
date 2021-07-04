{%- assign device = site.data.devices[page.device] -%}
{% if device.custom_recovery_codename %}
{% assign custom_recovery_codename = device.custom_recovery_codename %}
{% else %}
{% assign custom_recovery_codename = device.codename %}
{% endif %}

## 通过 `fastboot` 临时启动到自定义 Recovery

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
    {% include alerts/tip.html content="一些设备在进入 Bootloader 后对 USB 的支持可能会有点问题, 如果你在运行 `fastboot` 命令时卡住，没有返回值，例如 `fastboot getvar .. `, `fastboot boot ...`, `fastboot flash ...` 那可能需要换一个 USB 口连接设备 (最好是 USB 2.0) 或者是通过 USB 扩展坞连接。" %}

5. 在设备上临时刷入一个 Recovery 镜像:
```
fastboot flash boot <recovery_filename>.img
```
    {% include alerts/note.html content="老版本的 fastboot 可能对 A/B 兼容性不是很好。如果你在使用老版本 fastboot 刷入 `boot_a` / `boot_b` 时，他可能会识别为 `boot__a` / `boot__b` 这时可能就会刷入失败。如果遇到这种情况，则必须升级 `fastboot` 到 `31.0.2` 或更新版本。" %}
    {% include alerts/tip.html content="该文件的名称可能与此命令中的名称不一致，可能需要进行调整。" %}
6. {{ device.recovery_boot }}

{% unless site.data.devices[page.device].no_fastboot_boot %}
{% if device.uses_twrp %}
    在一些设备中，可以直接使用 fastboot 引导镜像启动，只需运行:
```
fastboot boot <recovery_filename>.img
```
    {% include alerts/tip.html content="该文件的名称可能与此命令中的名称不一致，可能需要进行调整。" %}
{% endif %}
{% endunless %}
