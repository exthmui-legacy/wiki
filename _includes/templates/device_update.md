{% assign device = site.data.devices[page.device] %}

## 升级你的设备

{% include alerts/note.html content="我们强烈建议使用我们内置的系统更新应用，不过，也允许使用其他方法。"%}

{% if device.is_ab_device %}
{% include alerts/note.html content="addon.d-v2/backuptool_ab 等负责备份附加组件(GApps/SU)的工具，对 A/B 分区方案（无缝更新）设备上不起作用。只有当你通过内置的系统更新应用进行更新时，附加组件才会得到备份。" %}
{% endif %}

## 使用 exTHmUI 系统更新应用

1. 打开设置, 进入 "系统", 然后进入 "系统更新"。
2. 点击右上角的刷新图标。
3. 选择你想要的更新，然后点击 "下载"。
{%- if device.is_ab_device %}
4. 下载完成后, 点击 "安装"。当升级包安装完成后，系统更新应用将显示重启按钮，点击重启即可完成更新。
{%- else %}
4. 下载完成后, 点击 "安装"。你的设备将重启到 Recovery 来安装升级包，升级完成后，设备会自动重启。
{%- endif %}

## 使用你的电脑通过 push_update 脚本更新 (只适用于 Linux/macOS)
1. 确保电脑已经安装 `adb`。 安装向导可以在[这里]({{ "adb_fastboot_guide.html" | relative_url }})找到。
2. 在设备上打开 [USB 调试]({{ "adb_fastboot_guide.html#setting-up-adb" | relative_url }})。
此外，打开 "设置"，点击 "系统"，然后点击 "开发者选项"，然后勾选 "Root 身份的调试"（exTHmUI 10 或更高版本）或点击 "Root 授权"，然后选择 "仅限于 ADB"。
3. 运行 `adb root`
{%- if device.current_branch < 15 %}
4. 运行 `wget https://raw.githubusercontent.com/LineageOS/android_packages_apps_Updater/cm-{{ device.current_branch }}/push-update.sh && chmod +x push-update.sh`
{%- else %}
4. 运行 `wget https://raw.githubusercontent.com/LineageOS/android_packages_apps_Updater/lineage-{{ device.current_branch }}/push-update.sh && chmod +x push-update.sh`
{% endif %}
5. 运行: `./push-update.sh /path/to/zip`
{%- if device.is_ab_device %}
6. 打开设置, 进入 "系统", 然后进入 "系统更新"。点击 "安装"。当升级包安装完成后，系统更新应用将显示重启按钮，点击重启即可完成更新。
{%- else %}
6. 打开设置, 进入 "系统", 然后进入 "系统更新"。然后点击新推送更新上的 "安装"，开始安装。你的设备将重启到 Recovery 来安装升级包，升级完成后，设备会自动重启。
{% endif %}

{% unless device.is_ab_device%}
## 通过 Recovery 更新
1. 确保电脑已经安装 `adb`。 安装向导可以在[这里]({{ "adb_fastboot_guide.html" | relative_url }})找到。
2. 在设备上打开 [USB 调试]({{ "adb_fastboot_guide.html#setting-up-adb" | relative_url }})。
此外，打开 "设置"，点击 "系统"，然后点击 "开发者选项"，然后勾选 "Root 身份的调试"（exTHmUI 10 或更高版本）或点击 "Root 授权"，然后选择 "仅限于 ADB"。
5. 运行: `adb reboot sideload`
6. 运行: `adb sideload /path/to/zip`
{% if device.uses_twrp %}
7. 运行: `adb reboot`
{% else %}
7. 点击屏幕左上方的返回箭头，然后点击 "Reboot system now".
{% endif %}
{% endunless %}
