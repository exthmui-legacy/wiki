{% assign device = site.data.devices[page.device] %}

## 基本要求

{% include alerts/important.html content="在实际操作之前，请至少阅读一遍说明，以避免因遗漏的步骤而出现任何问题！" %}

1. 确保你的电脑已经安装 `adb`{% unless device.install_method == 'heimdall' or device.install_method == 'dd' %} 和 `fastboot`{% endunless %}。安装向导可以在[这里]({{ "adb_fastboot_guide.html" | relative_url }})找到。
2. 在你的设备上打开 [USB 调试]({{ "adb_fastboot_guide.html#setting-up-adb" | relative_url }})。

{%- if device.before_install %}
{% capture path %}templates/device_specific/{{ device.before_install }}.md{% endcapture %}
{% include {{ path }} %}
{%- endif %}

{% if device.required_bootloader %}
## 针对该机型的额外要求

{%- capture bootloader %}
你设备的 Bootloader 版本必须为 {% for el in device.required_bootloader %} {% if forloop.last %} `{{ el }}` {% else %} `{{ el }}` / {% endif %} {% endfor %}, 否则不适用于本页。
可以通过在终端或 `adb shell` 运行 `getprop ro.bootloader` 来检查你设备的 Bootloader 版本。
{% endcapture %}
{% include alerts/warning.html content=bootloader %}
{%- endif %}

{%- if device.install_method %}
{% capture recovery_install_method %}templates/recovery_install_{{ device.install_method }}.md{% endcapture %}
{% include {{ recovery_install_method }} %}
{%- else %}
## 解锁 Bootloader / 安装自定义 Recovery

对于停止维护的设备，没有 Recovery 安装说明。
{%- endif %}

{%- if device.before_lineage_install %}
{% capture path %}templates/device_specific/{{ device.before_lineage_install }}.md{% endcapture %}
{% include {{ path }} %}
{%- endif %}

## 通过 Recovery 安装 exTHmUI

{%- capture userspace_architecture -%}
{%- if device.architecture.userspace -%}
{{ device.architecture.userspace }}
{%- else -%}
{{ device.architecture }}
{%- endif -%}
{%- endcapture -%}

{%- if device.maintainers != empty %}
1. 下载你想要的 [exTHmUI 安装包](https://download.exthmui.cn/exthmui/{{ device.codename }})或自己[编译]({{ "devices/" | append: device.codename | append: "/build" | relative_url }})安装包。
{%- else %}
1. [编译]({{ "devices/" | append: device.codename | append: "/build" | relative_url }}) exTHmUI 安装包
{%- endif %}
    * 可以选择同时下载附加组件如 Google Apps。
2. 如果你不在 Recovery, 请重启进入 Recovery:
    * {{ device.recovery_boot }}
    {% if device.vendor == "LG" %}
        {% include templates/recovery_boot_lge.md %}
    {% endif %}
{%- if device.uses_twrp %}
3. 点击 **Wipe**.
4. 点击 **Format Data** 这将移除加密，并删除存储在内部存储中的所有文件。
{%- if device.is_ab_device %}
{%- else %}
5. 返回上一级菜单并点击 **Advanced Wipe**, 选择 *Cache* 和 *System* 分区，然后 **Swipe to Wipe**.
{%- endif %}
6. 安装 exTHmUI `.zip` 安装包:
    * 在设备上点击 "Advanced", "ADB Sideload", 然后滑动来启动 Sideload。
    * 在电脑上使用此命令来安装 exTHmUI 到设备 `adb sideload filename.zip`
        {% include alerts/tip.html content="如果安装成功，输出将在 47% 处停止，并返回 `adb: failed to read command: Success`." %}
{%- else %}
3. 现在点击 **Factory Reset**, 然后点击 **Format data / factory reset**，这将移除加密，并删除存储在内部存储中的所有文件，然后格式化缓存分区（如果有的话）。
5. 返回主菜单。
6. 安装 exTHmUI `.zip` 安装包:
    * 在设备上点击 "Apply Update", 然后点击 "Apply from ADB" 来启动 sideload。
    * 在电脑上使用此命令来安装 exTHmUI 到设备 `adb sideload filename.zip`
        {% include alerts/tip.html content="如果安装成功，输出将在 47% 处停止，并返回 `adb: failed to read command: Success`." %}
{%- endif %}
{%- if device.is_ab_device and device.uses_twrp %}
7. _(可选)_: 如果你想安装附加组件, 运行 `adb reboot sideload`, 然后运行 `adb sideload filename.zip` 即可。
{%- elsif device.is_ab_device %}
7. _(可选)_: 如果你想安装附加组件, 点击 `Advanced`, 然后点击 `Reboot to Recovery`, 当你的设备重启后, 点击 `Apply Update`, 然后点击 `Apply from ADB`, 然后运行 `adb sideload filename.zip` 即可。
{%- else %}
7. _(可选)_: 如果你想安装附加组件, 重复一遍安装 exTHmUI 安装包的步骤即可，`filename` 记得换成附加组件的。
{%- endif %}
{% if device.is_ab_device or device.uses_twrp != true %}
    {% include alerts/note.html content="附加组件没有使用 exTHmUI 的密钥进行签名, 因此当安装时, Lineage Recovery 将会报告 `Signature verification failed`, 这是正常的，点击 `Continue` 即可继续安装。" %}
{%- endif %}
    {% include alerts/note.html content="如果你想在你的设备上安装 GApps, 你必须在启动 exTHmUI **之前** 安装 GApps" %}
{%- if device.current_branch >= 10 %}
{%- if device.uses_twrp and device.is_ab_device != true %}
8. 所有东西都安装完成后，运行 `adb reboot`。
{%- else %}
8. 所有东西都安装完成后, 点击屏幕左上方的返回箭头, 然后点击 "Reboot system now"。
{%- endif %}
{%- else %}
{%- if device.uses_twrp and device.is_ab_device != true %}
8. _(可选)_: 可以通过安装 [exTHmUI' AddonSU](https://download.exthmui.cn/exthmui/extras)(使用 `{{ userspace_architecture }}` 版本) Root 你的设备, 或者使用你喜欢的其他工具 Root。
9. 所有东西都安装完成后，运行 'adb reboot'。
{%- else %}
8. _(可选)_: 可以通过安装 [exTHmUI' AddonSU](https://download.exthmui.cn/exthmui/extras)(使用 `{{ userspace_architecture }}` 版本) Root 你的设备, 或者使用你喜欢的其他工具 Root。
9. 所有东西都安装完成后, 点击屏幕左上方的返回箭头, 然后点击 "Reboot system now"。
{%- endif %}
{%- endif %}

{% include alerts/specific/warning_recovery_app.html %}
