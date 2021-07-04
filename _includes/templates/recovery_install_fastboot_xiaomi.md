{%- assign device = site.data.devices[page.device] %}
{%- unless device.no_oem_unlock_switch %}
## 解锁 Bootloader

{% include alerts/note.html content="设备只需执行一次解锁步骤。" %}
{% include alerts/warning.html content="解锁 Bootloader 可能会清空你设备上的所有数据，在这之前请确保你的重要数据已经备份。注意！OEM 厂商的备份文件可能无法在 exTHmUI 中被恢复。" %}
{% include alerts/tip.html content="在解锁之前，强烈建议在设备上安装最新版 MIUI" %}
{% include alerts/note.html content="你需要一台运行 Windows 7 或更新版本 Windows 的电脑才可以继续操作" %}

1. 在[小米官网](https://global.account.xiaomi.com/pass/register)注册一个小米账号。
    {% include alerts/tip.html content="需要小米帐号才能解锁 Bootloader。注意，一个帐户每个月只能解锁一个设备。" %}
2. 在你的小米账户中绑定电话号码。
3. 将SIM卡插入设备。
4. 进入 `设置` > `关于手机` 然后连续点击 `MIUI 版本` 来打开开发者选项。
5. 在 `设置` > `高级设置` > `开发者选项` > `解锁状态` 中绑定你的小米账号。
6. 下载[小米解锁工具](https://en.miui.com/unlock/download_en.html) (必须使用 Windows 来运行)。
7. 运行小米解锁工具，并按照工具提供的说明操作。
    {% include alerts/note.html content="该工具可能会告诉你，你必须等待长达 30 天的时间。如果是这样，请等待所需的时间，然后继续下一步。" %}
8. 在设备和小米账户验证成功后，Bootloader 将会被解锁。
9. 由于设备完全重置，您将需要重新启用 USB 调试以继续。

{% endunless %}

{% if device.before_recovery_install %}
{% capture path %}templates/device_specific/{{ device.before_recovery_install }}.md{% endcapture %}
{% include {{ path }} %}
{% endif %}

{% include templates/recovery_install_fastboot_generic.md %}
{% if device.no_oem_unlock_switch %}
{% include alerts/tip.html content="在安装自定义 Recovery 之前，强烈建议在设备上安装最新版 MIUI" %}
{% endif %}
