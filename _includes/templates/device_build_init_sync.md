输入以下命令来同步源码:

{% include alerts/note.html content="确保你同步的分支是你需要的" %}

```
cd ~/android/lineage
repo init -u https://github.com/exTHmUI/android.git -b exthm-{{ current_branch }}
```

### 同步源码

输入以下命令来同步源码:

```
repo sync
```

exTHmUI 的清单中设置了合理的repo默认配置，我们强烈建议你使用这个配置。(意思就是不要添加任何额外参数，例如 `-j8`)

我们的同步的默认额外参数为 `-j4` 和 `-c`。

`-j4` 是指使用四线程同步源码，`-c` 是指只拉取当前分支。

如果你在同步是遇到问题，可以尝试降低线程。

{% include alerts/note.html content="取决你的网速，这可能需要一点时间" %}

{% include alerts/tip.html content="`repo sync` 是用来从 exTHmUI 和 Google 获取最新的源码。
如果你想更新源码，运行它就好了。注意，如果你对源码进行了更改，则 `repo sync` 可能会清空你的更改" %}