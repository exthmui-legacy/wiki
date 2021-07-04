{%- assign device = site.data.devices[page.device] -%}
{% if device.custom_recovery_codename %}
{% assign custom_recovery_codename = device.custom_recovery_codename %}
{% else %}
{% assign custom_recovery_codename = device.codename %}
{% endif %}

## Root 你的设备

{% include alerts/important.html content="设备必须 Root 才能进行下一步操作。" %}

{% case device.root_method[0] %}
{% when 'custom' %}
1. 参考[此向导]({{ device.root_method[1] }}) Root 你的设备。
{% when 'kingroot' %}
1. 在[这里下载](https://kingroot.net/) Kingroot。
   1. 安装并运行 Kingroot 来 Root 你的设备。
{% when 'towelroot' %}
1. 在[这里下载](https://towelroot.com/) TowelRoot。
   1. 点击 “入” 来下载 TowelRoot。
   2. 安装并运行 TowelRoot 来 Root 你的设备。
{% endcase %}

{% if device.before_dd_recovery %}
{% capture path %}templates/device_specific/{{ device.before_dd_recovery }}.md{% endcapture %}
{% include {{ path }} %}
{% endif %}

## 使用 `dd` 来安装自定义 Recovery

{% if device.custom_recovery_link %}
1. 你可以在[这里下载]({{ device.custom_recovery_link }})自定义 Recovery。
{% else %}
{% if device.uses_twrp %}
1. 你可以在[这里下载](https://dl.twrp.me/{{ custom_recovery_codename }})最新版本的 TWRP，文件名应该看起来像这样 `twrp-x.x.x-x-{{ custom_recovery_codename }}.img`。
{% else %}
1. 你可以在[这里下载](https://download.exthmui.cn/exthmui/{{ custom_recovery_codename }}) exTHmUI Recovery。
{% endif %}
{% endif %}
2. 将 Recovery 镜像放在 `/sdcard`:
   * 使用 adb 运行: `adb push <recovery_filename>.img /sdcard/<recovery_filename>.img`
    {% include alerts/tip.html content="该文件的名称可能与此命令中的名称不一致，可能需要进行调整。" %}
   * 你可以使用任何你觉得合适的方法。`adb` 在所有 Android 设备上都是可用的，可以在 Android 和 Recovery 下工作。
3. 现在运行 `adb shell` 然后运行:
```
su
dd if=/sdcard/<recovery_filename>.img of={{ device.recovery_partition }}
```
4. 重启到 Recovery
    * 在同一窗口运行:
```
reboot recovery
```
