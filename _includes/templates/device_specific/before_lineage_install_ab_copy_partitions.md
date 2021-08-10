## 预安装流程

在某些情况下插槽 `b` 可能是空的或者和插槽`a`中的固件版本相比过老，从而导致包括潜在的成"硬砖"在内的各种问题。 我们可以通过将插槽 `a` 的内容复制到插槽 `b` 来确保这些都不会发生。 这时该步骤**<u>必须进行</u>**。

要进行该操作，通过如下步骤旁加载补丁包`copy-partitions-20210323_1922.zip` ：

1. 从[这里](https://www.androidfilehost.com/?fid=2188818919693768129)下载 `copy-partitions-20210323_1922.zip` 
{%- if device.uses_twrp %}
2. 旁加载补丁包`copy-partitions-20210323_1922.zip` ：
    * 在Recovery模式下选择“高级”，再选择"ADB Sideload"然后滑动开启sideload（旁加载）
    * 在主机上使用命令：`adb sideload copy-partitions-20210323_1922.zip`旁加载补丁包
    {%- else %}
2. 旁加载补丁包`copy-partitions-20210323_1922.zip` ：
    * 在Recovery模式下选择 "Apply Update"，再选择"Apply from ADB" 开启sideload（旁加载）
    * 在主机上使用命令：`adb sideload copy-partitions-20210323_1922.zip`旁加载补丁包
    * {% include alerts/note.html content="这个分区复制脚本由LineageOS开发者erfanoabdi和filipepferraz创作但是它并没有用任何LineageOS或基于LineageOS（包括exTHmUI）的ROM的密钥进行签名，因此当这个脚本被旁加载时，Lineage Recovery将展现一个写着 `Signature verification failed`的屏幕，这是正常现象，直接点 `Continue`." %}
3. 现在请重启到Recovery。
{%- endif %}
