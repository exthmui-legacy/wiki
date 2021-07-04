{%- assign device = site.data.devices[page.device] -%}
{% if device.custom_recovery_codename %}
{% assign custom_recovery_codename = device.custom_recovery_codename %}
{% else %}
{% assign custom_recovery_codename = device.codename %}
{% endif %}

## 通过 `edl` 模式安装自定义 Recovery

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
adb reboot edl
```
    {% if device.edl_boot %}
    你也可以使用组合键进入 edl 模式:

    * {{ device.edl_boot }}
    {% endif %}
4. 参考[此向导]({{ device.install_recovery_guide }})刷入 Recovery。
