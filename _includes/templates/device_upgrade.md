{% assign device = site.data.devices[page.device] %}

{% include alerts/important.html content="在实际操作之前，请至少阅读一遍说明，以避免因遗漏的步骤而出现任何问题！" %}

{% capture upgrade_only %}这些说明只适用于版本升级。如果你想降级到早期版本, 请参考[设备安装]({{ "devices/" | append: device.codename | append: "/install" | relative_url }})向导。{% endcapture %}
{% include alerts/warning.html content=upgrade_only %}

{%- capture userspace_architecture -%}
{%- if device.architecture.userspace -%}
{{ device.architecture.userspace }}
{%- else -%}
{{ device.architecture }}
{%- endif -%}
{%- endcapture -%}

## 手动升级 exTHmUI

{%- unless device.is_ab_device %}
{%- capture recovery_update %}在某些情况下，新版本的 exTHmUI 可能会因为过时的 Recovery 而无法安装。
请参考[设备安装向导]({{ "devices/" | append: device.codename | append: "/install" | relative_url }})来升级 Recovery。{% endcapture %}
{% include alerts/tip.html content=recovery_update %}
{%- endunless %}

{%- for version in device.versions %}
{%- if version < 10 %}
{%- capture devOptions -%}
打开 "设置"，点击 "系统"，然后点击 "开发者选项"，然后点击 "Root 授权"，然后选择 "仅限于 ADB"。然后运行 `adb root`'
{%- endcapture -%}
{%- break %}
{%- endif %}
{%- endfor %}

系统更新应用不支持从一个版本的 exTHmUI 升级到另一个版本，并且会阻止安装到不同版本的任何更新。手动升级需要的步骤与首次安装 exTHmUI 类似。

{%- if device.maintainers != empty %}
1. 下载你想要的 [exTHmUI 安装包](https://download.exthmui.cn/exthmui/{{ device.codename }})或自己[编译]({{ "devices/" | append: device.codename | append: "/build" | relative_url }})安装包。
{%- else %}
1. [编译]({{ "devices/" | append: device.codename | append: "/build" | relative_url }}) exTHmUI 安装包。
{%- endif %}
    * 可以选择同时下载附加组件如 Google Apps。
2. 确保电脑已经安装 `adb`。 安装向导可以在[这里]({{ "adb_fastboot_guide.html" | relative_url }})找到。
3. 在设备上打开 [USB 调试]({{ "adb_fastboot_guide.html#setting-up-adb" | relative_url }})。
{%- if device.format_on_upgrade %}
4. 通过运行 `adb reboot recovery` 来使设备重启到 Recovery, 或者这样做:
    * {{ device.recovery_boot }}
5. 抹除 data 分区
    {% include alerts/warning.html content="如果不进行抹除，你的设备将无法在新版本上启动！" %}
{%- if device.uses_twrp != true %}
6. 点击 `Advanced`, 然后点击 `Enable ADB`.
{%- endif %}
{%- endif %}
4. 运行 `adb reboot sideload`.
    {% include alerts/important.html content="设备重启后可能会黑屏，不要怕，这是某些 Recovery 中的一个已知错误，接下来继续按说明操作。" %}
5. 运行 `adb sideload /path/to/zip` (exTHmUI 安装包的路径).
{%- if device.is_ab_device and device.uses_twrp %}
6. _(可选)_: 如果你想安装附加组件, 运行 `adb reboot sideload`, 然后运行 `adb sideload filename.zip` 即可。
{%- elsif device.is_ab_device %}
6. _(可选)_: 如果你想安装附加组件, 点击 `Advanced`, 然后点击 `Reboot to Recovery`, 当你的设备重启后, 点击 `Apply Update`, 然后点击 `Apply from ADB`, 然后运行 `adb sideload filename.zip` 即可。
{%- elsif device.uses_twrp %}
6. _(可选)_: 如果你想安装附加组件, 点击 `Advanced`, 然后点击 `ADB Sideload`, 然后滑动来开始 sideload, 然后运行 `adb sideload filename.zip` 即可。
{%- else %}
6. _(可选)_: 如果你想安装附加组件, 点击 `Apply Update`, 然后点击 `Apply from ADB`, 然后运行 `adb sideload filename.zip` 即可。
{%- endif %}
    {% include alerts/note.html content="如果你想在你的设备上安装 GApps, 你必须在启动 exTHmUI **之前** 安装 GApps。如果你没有安装 GApps，你必须抹除 data 分区（或恢复出厂设置）后启动" %}

{% if device.current_branch >= 10 %}
{% if device.uses_twrp and device.is_ab_device != true %}
7. 所有东西都安装完成后，运行 `adb reboot`。
{% else %}
7. 所有东西都安装完成后, 点击屏幕左上方的返回箭头, 然后点击 "Reboot system now"。
{% endif %}
{% else %}
{% if device.uses_twrp and device.is_ab_device != true %}
7. _(可选)_: 可以通过安装 [exTHmUI' AddonSU](https://download.exthmui.cn/exthmui/extras)(使用 `{{ userspace_architecture }}` 版本) Root 你的设备, 或者使用你喜欢的其他工具 Root。
8. 所有东西都安装完成后，运行 `adb reboot`。
{% else %}
9. _(可选)_: 可以通过安装 [exTHmUI' AddonSU](https://download.exthmui.cn/exthmui/extras)(使用 `{{ userspace_architecture }}` 版本) Root 你的设备, 或者使用你喜欢的其他工具 Root。
10. 所有东西都安装完成后, 点击屏幕左上方的返回箭头, 然后点击 "Reboot system now"。
{% endif %}
{% endif %}

{% include alerts/specific/warning_recovery_app.html %}
