### 你需要

{% if device -%}
* 一台 {{ device.vendor }} {{ device.name }}.
{%- endif %}
* 一台配置较好的 64 位电脑，存储空间至少需要 200 GB，RAM 至少 16 GB
* 良好的网络环境
* 懂一定的 Android 术语和 Linux 命令.
 了解一些基本的命令行概念很有用，例如 `cd`，它代表 "改变目录"，目录层次的概念，以及在 Linux 中它们是由 `/` 分隔的，等等。

{% include alerts/tip.html content="如果你不习惯使用Linux，这是一个很好的学习机会。它是免费的，只需下载并运行一个虚拟机（VM），如 [VirtualBox](https://www.virtualbox.org), 然后安装一个 Linux 发行版，如 [Ubuntu](https://www.ubuntu.com)。
任何最新版本的 64 位 Ubuntu 都没问题，但我们建议使用最新的长期支持（LTS）版本。关于使用 VirtualBox 来运行 Ubuntu 在网络上有很多说明，所以我们把这个问题留给你。如果你已经在使用 Linux 或 macOS，你可以直接进入下一步。" %}

让我们开始！

## 编译 exTHmUI

{% if device %}
{% include alerts/note.html content="这些步骤你只需要做一次。如果你已经配置好了编译环境并同步了源代码,
转到[准备设备代码](#prepare-the-device-specific-code)" %}
{% else %}
{% include alerts/note.html content="这些步骤你只需要做一次。如果你已经配置好了编译环境并同步了源代码,
转到[开始编译](#start-the-build)" %}
{% endif %}

### 安装 platform-tools

如果你还没有安装 `adb` 和 `fastboot`, 你可以从 [Google 下载](https://dl.google.com/android/repository/platform-tools-latest-linux.zip)，
然后解压并运行:

```
unzip platform-tools-latest-linux.zip -d ~
```

{% include alerts/tip.html content="文件的名称可能与此命令中的名称不同，需要做相应调整。" %}

现在需要将 `adb` 和 `fastboot` 添加到环境变量 (PATH) 中. 打开 `~/.profile` 然后添加如下内容:

```
# add Android SDK platform tools to path
if [ -d "$HOME/platform-tools" ] ; then
    PATH="$HOME/platform-tools:$PATH"
fi
```

然后运行 `source ~/.profile` 来更新你的环境变量

### 安装编译 exTHmUI 所需的依赖包

编译 exTHmUI 依赖于几个软件包。你可以使用你 Linux 发行版的软件包管理器安装这些软件包。

{% include alerts/tip.html content="[软件包管理器](https://en.wikipedia.org/wiki/Package_manager) 是 Linux 中用于安装或删除软件用的软件。在 Ubuntu 中，你可以直接使用 Ubuntu 软件中心，你也可以直接在终端使用 `apt-get` 命令。" %}

{%- capture cpu_architecture %}
{%- if device.architecture.cpu -%}
   {{ device.architecture.cpu }}
{%- else -%}
   {{ device.architecture }}
{%- endif -%}
{%- endcapture -%}

编译 exTHmUI 需要:

* `bc bison build-essential ccache curl flex g++-multilib gcc-multilib git gnupg gperf imagemagick
   lib32ncurses5-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses5 libncurses5-dev
   libsdl1.2-dev libssl-dev libxml2 libxml2-utils lzop pngcrush rsync
   schedtool squashfs-tools xsltproc {% if cpu_architecture contains 'x86' %}yasm {% endif %}
   zip zlib1g-dev`

{% if device.versions contains 13.0 %}
要编译 exTHmUI 13.0, 还需要:

* `maven`
{% endif %}

如果 Ubuntu 版本低于 20.04 (focal), 还需要:

* `libwxgtk3.0-dev`

如果 Ubuntu 版本低于 16.04 (xenial), 还需要:

* `libwxgtk2.8-dev`

#### Java

不同版本的 exTHmUI 依赖于不同版本的 JDK (Java 开发套件) 版本。

* exTHmUI 11: OpenJDK 11 (包含在源码中)
* exTHmUI 9-10: OpenJDK 1.9 (包含在源码中)

### 新建文件夹

你需要在你的构建环境中新建一些目录。

这样来新建:

```
mkdir -p ~/bin
mkdir -p ~/android/lineage
```

`~/bin` 目录将包含 git-repo（通常称为 "repo"），`~/android/lineage` 目录将包含 exTHmUI 的源代码。

### 安装 `repo` 的命令

运行以下命令来下载 `repo` 二进制文件，并使其可执行:

```
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

### 将 `~/bin` 加入到环境变量

在最近版本的 Ubuntu, `~/bin` 应该已经被添加到环境变量。

你可以打开 `~/.profile` 来检查是否已被添加 (如果没被添加，请手动添加):

```
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi
```

然后运行 `source ~/.profile` 来更新你的环境变量


### 配置 git
`repo` 需要鉴定你的身份以同步 Android 源代码, 运行以下命令来配置你的 `git` 身份:
```
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

### 打开缓存来加速编译

如果你想加快后继编译的速度，你可以打开 [`ccache`](https://ccache.samba.org/)：

```
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache
```

将这些加入到 `~/.bashrc`，然后通过输入以下命令设置你希望 `ccache` 使用的最大磁盘空间：

```
ccache -M 50G
```

其中 `50G` 相当于 50GB 的缓存。这需要编译一次。设置为 25 GB-100 GB 都会让编译速度明显增加
(例如，1 小时的编译时间可以减少到 20 分钟。). 如果你只为一台设备编译, 那么 25 GB-50 GB 是个不错的选择。
如果你编译好几台不同的设备，那么设置为 75 GB-100 GB 比较好。
注意，这些缓存不会被自动移除。

你也可以启用 `ccache` 压缩。虽然这可能会让性能稍微减慢，但它增加了缓存中的文件数量。
要打开的话运行:

```
ccache -o compression=true
```

{% include alerts/note.html content="如果启用了压缩功能，`ccache` 的大小可以更小。" %}


{% if device.current_branch >= 14 and device.current_branch < 16 %}
### 配置 Jack

[Jack](http://source.android.com/source/jack.html) 是用于编译 exTHmUI 14.1 和 15.1 的 Java 工具链。 众所周知，如果配置不正确，它的内存会经常耗尽。- 一个简单的解决方法是运行这个命令:

```
export ANDROID_JACK_VM_ARGS="-Dfile.encoding=UTF-8 -XX:+TieredCompilation -Xmx4G"
```

把这个命令加入到 `~/.bashrc` 将自动配置 Jack 以分配足够的内存数量。
{% endif %}
