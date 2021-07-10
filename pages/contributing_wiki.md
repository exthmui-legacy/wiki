---
sidebar: home_sidebar
title: 贡献维基
folder: meta
permalink: contributing_wiki.html
---

所以，你已经决定好要编辑维基了，やりますね！这个页面会引导你开始，请根据最适合你的说明来进行操作。

## 克隆维基的源码

### 使用 repo

如果你已经有了一个检出的 exTHmUI 源码，那么这一步你就已经做完了。维基的Git仓库的存放位置在 `$EXTHM_SRC/wiki` 下。

### 使用 git

如果在你的设备上没有一个可用的 exTHmUI 源码树，那么你可以直接从 Github 拉取源码到指定的任意位置。在这个例子中，我们将把维基的源码克隆到 `~/exthm_wiki` 下。

#### 安装 git

##### Windows

[Git for Windows](https://git-for-windows.github.io/).

##### macOS

[Git installer](https://git-scm.com/download/mac).

##### Linux

你可以通过以下命令在Ubuntu上安装 Git:

```
sudo apt-get install git
```

对于不同的发行版，更详细的指示可以在 [这里](https://git-scm.com/download/linux) 找到。

#### 设置 Git

运行以下命令来完成你的身份设置:

```
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@例子.com"
```

{% include alerts/tip.html content="在这一步之前，你可以复刻 exTHmUI 维基 的仓库，然后在你自己的复刻版本上进行贡献，你也可以通过 Github Pages 来实时预览你的更改。对于这一步，在 [下面](#使用-github-进行预览) 我们有更详细的说明。你只需要复刻维基的仓库，并且在 `git clone` 命令中把 exTHmUI 替换成你的 Github 用户名 即可。" %}

```
git clone https://github.com/exTHmUI/wiki ~/exthm_wiki
cd ~/exthm_wiki
curl -Lo .git/hooks/commit-msg https://review.exthmui.cn/tools/hooks/commit-msg
chmod +x .git/hooks/commit-msg
```

## 制作和预览更改

你现在可以使用你最爱的文本编辑器在这个文件夹中进行更改了。如果你需要将你的文件夹恢复到一个可用的状态，移除你的更改，只需要运行：

```
git reset --hard HEAD
```

### 使用 Github 进行预览

这大概是最简单的方法，但需要你复刻并克隆维基的仓库。

#### 提交并预览你的更改

运行以下命令:

```
git add .
git commit
```

你将会在你的终端中看到一个编辑器。在第一行，输入一个简短的 (80 个字符以下，英文) 更改信息。如果你想要更详细的注释，在第一行
下面空出一格，并尽情发挥。如下：
```
Add the contributing page

The contributing page will show people how they can edit our wiki, which
is important, since it wouldn't be much of a wiki without editors.
```
保存你的文件，并且离开编辑器。现在，你可以上传你的更改到 Github 了:

```
git push origin
```

现在你可以预览你的更改 - 你的复刻版本将在 `http://你的用户名.github.io/wiki` 下呈现。

#### 修复错误

所以，你已经做出了你的更改，并且你可以在 Github Pages 上预览你复刻的维基。但有东西出错了！你有一个拼写错误，
或者不小心删除掉了什么东西。不用害怕，你可以做出更改！

首先，修复你做出的错误。然后将他们推送到 Github:

```
git add .
git commit --amend
```

你的提交信息应该会出现在一个编辑器中。你可以编辑它，或者退出编辑器。

运行以下指令来提交到远程仓库：

```
git push --force origin
```

### 使用 docker 来进行本地预览

{% include alerts/warning.html content="Docker 需要一个 64位元 的系统。如果你没有一个 64位元 的系统，请不要继续。" %}

#### 安装 docker

添加 docker 的钥匙环，并且安装docker的包:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install docker-ce
```

添加你的账户到 _docker_ 组里，这样不需要 `sudo` 前缀，即可运行docker:

```
sudo gpasswd -a $USER docker
newgrp docker
```

退出你的账户再登陆回来，这样子能让组设置生效。

#### 构建 docker 映像

以下命令构建了 docker 镜像。这些命令通常只需要执行一遍:

```
cd $EXTHM_SRC/wiki #Or to wherever you cloned the repository
docker build -t wiki .
```

#### 编辑维基

每一次你需要编辑维基的时候，你都需要打开一个运行Jekyll的本地服务器:

```
cd $EXTHM_SRC/wiki
docker run -p 4000:4000 -v $(pwd):/src -it wiki
```

运行完这些命令之后，你便可以通过 [本地 Jekyll 服务器](http://127.0.0.1:4000) 进行更改。

### 使用 rvm 和 jekyll 进行本地预览

#### 安装 `rvm`

`rvm` 在分离 ruby 不同的使用方法上是一个很好的工具。在这一步中，你将会安装 `ruby` 和一堆 ruby 的模块 (gems)。这些模块将会与在你的设备上的其他 ruby 使用实例分离开来 (不管是现在还是未来)。

如果你没有安装 `rvm`，输入以下命令来安装它:

```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
curl -sSL https://get.rvm.io | bash -s stable
```

#### 为了编辑维基来设置 `ruby`

这些步骤将通过 `rvm` 命令来设置并安装最新版本的 ruby MRI。所有的 gems (模块) 都被储存在命名空间 `exthm_wiki` 中，并且你的环境将设置为允许远程连接到Github。一旦设置成功，`ruby` 将被安装完成，并且所有的 gems 都会被下载完成。
```
cd $EXTHM_SRC/wiki
echo ruby > .ruby-version
echo wiki > .ruby-gemset
rvm install ruby
gem install bundler rails
bundle install
```

#### 编辑维基

每当你想要编辑维基的时候，你需要启动一个运行着 Jekyll 的本地服务器:

```
source ~/.rvm/scripts/rvm
cd $EXTHM_SRC/wiki
bundle exec jekyll serve --incremental
```

完成了这些后，你将可以通过 [本地 Jekyll 服务器](http://127.0.0.1:4000) 预览你的更改。

{% include alerts/tip.html content="增加的标记会导致 Jekyll 只会在你更改个别页面时改变个别界面的内容 (没有增加的标记的话，当你做任意更改时，整个站点将会被重新生成。取决于你的硬件条件，这会导致 10-30 秒的时间延迟) 。当编辑页面信息时，这会非常有用。但当你编辑 data/device/name.yml 时，这可能会有点恼人。当编辑那些文件时，你需要保存你的数据，之后创建你正在浏览器里观看的界面所对应的 `.md` 文件。一个解决该问题的简单方式是: `touch pages/*/<devicename>*`" %}

{% include alerts/note.html content="如果你正在一个远端服务器上工作，并且你信任你连接的网络，你可以添加 `--host <fully-qualified hostname> ` 这项参数到 Jekyll 的命令行中，并且允许远端链接到你的服务器。" %}


## 上传你的更改

exTHmUI 使用 [Gerrit](https://review.exthmui.cn) 审核服务器 来审核建议的更改。在你登录之前，你需要
[注册一个账户](https://review.exthmui.cn/login/%2Fq%2Fstatus%3Aopen%2B-is%3Awip)，然后[设置你的SSH密钥](https://review.exthmui.cn/Documentation/user-upload.html#ssh)。

在你作完这些之后，你可以将你的更改提交到 Gerrit:

```
git remote add gerrit ssh://<gerritusername>@review.lineageos.org:29418/exTHmUI/lineage_wiki
git push gerrit HEAD:refs/for/master
```

在这之后，当你的更改看起来很完美时，有人会给它打分，并且将你的更改提交到维基。真棒！