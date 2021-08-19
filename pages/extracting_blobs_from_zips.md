---
sidebar: home_sidebar
title: 从 exTHmUI zip包中提取专有二进制blob文件
permalink: extracting_blobs_from_zips.html
---
## 介绍

专有blob可以从已经运行exTHmU的设备中提取，也可以从exTHmUI可安装的zip包中提取。在本指南中，我们将介绍从可安装的zip包中提取专有文件所需的步骤。

在开始之前，需要了解OTA类型之间的差异:

* **区块形式的OTA包(Block-based OTA)**: 系统分区的内容作为二进制数据存储在`.dat`或`.dat.br`文件中。
* **文件形式的OTA包(File-based OTA)**: 系统分区的内容位于zip包的`system`文件夹中
* **堆栈形式的OTA包(Payload-based OTA)**: 系统分区的内容作为`.img`文件存储在`payload.bin`中。

如果您的zip包中没有`system`文件夹,或者它几乎是空的,并且根目录存在名为`system.transfer.list的文件`，则您拥有的是区块形式的OTA包。在这种情况下，跳转到[从区块形式的OTA包中提取专有二进制blob文件](#从区块形式的OTA包中提取专有二进制blob文件) 。

如果`system`文件夹中包含系统分区的全部内容，而没有`system.transfer.list`，则您拥有的是文件形式的OTA包。请参见[从文件形式的OTA包中提取专有二进制blob文件](#从文件形式的OTA包中提取专有二进制blob文件)。

您还可能有一个堆栈形式的OTA包，如果您的设备使用A/B分区系统，它将使用这种形式的OTA。如果这是您所拥有的，请跳转到[从堆栈形式的OTA包中提取专有二进制blob文件](#从堆栈形式的OTA包中提取专有二进制blob文件)。

## 从区块形式的OTA包中提取专有二进制blob文件

一些区块形式的OTA包被拆分为多个文件，用于系统分区和其他分区，如vendor, product, oem, odm等。您可以通过在可安装exTHmUI zip包的根目录中为每个文件查找相应的`*.transfer.list`文件来确认您的文件是否已拆分。

如果您有一个拆分了的区块形式的OTA包，那么您将需要以类似如下所示的方式提取、解压缩和转换每个文件到system和vendor。

如果您没有拆分了的区块形式的OTA包，则可以跳过引用`vendor.transfer.list`和`vendor.new.dat.br`或`vendor.new.dat`的任何步骤

新建一个临时目录然后将工作目录移动到那里：

```
mkdir ~/android/system_dump/
cd ~/android/system_dump/
```

从exTHmUI可安装zip包中解压`system.transfer.list`和`system.new.dat.br`或`system.new.dat`：

```
unzip path/to/exthm-*.zip system.transfer.list system.new.dat*
```
`path/to/`是可安装zip包的路径

如果你的OTA包包含`vendor.transfer.list`和`vendor.new.dat.br`或`vendor.new.dat`（或者其他什么东西），也请从exTHmUI可安装zip包中将他们解压出来：

```
unzip path/to/exthm-*.zip vendor.transfer.list vendor.new.dat*
```
`path/to/`是可安装zip包的路径

对于存在`system.new.dat.br`/`vendor.new.dat.br`/等(一个[Brotli](https://zh.wikipedia.org/wiki/Brotli)文件)的情况，你应该先使用`brotli`实用程序对它们进行解压缩

```
sudo apt-get install brotli
brotli --decompress --output=system.new.dat system.new.dat.br
```

如果你有`vendor.dat.new.br`（或者其他的）文件：

```
brotli --decompress --output=vendor.new.dat vendor.new.dat.br
```

现在您需要获取`sdat2img`。此脚本可以将区块形式的OTA包内容转换为可以装载的转储镜像。 `sdat2img` 可在如下的git仓库找到，你可以通过如下指令clone这个仓库：

```
git clone https://github.com/xpirt/sdat2img
```

获得`sdat2img`之后，使用它提取系统映像:

```
python sdat2img/sdat2img.py system.transfer.list system.new.dat system.img
```

并且如果你有`vendor.dat.new.br`（或者其他的）文件：

```
python sdat2img/sdat2img.py vendor.transfer.list vendor.new.dat vendor.img
```

现在应该有一个名为`system.img`的文件，可以按如下方式挂载：

```
mkdir system/
sudo mount system.img system/
```

如果你有一个叫`vendor.img`的文件，你可以用如下方法挂载：

```
sudo rm system/vendor
sudo mkdir system/vendor
sudo mount vendor.img system/vendor/
```

现在，您还必须挂载其各自目录中的任何其他镜像文件。

装入镜像后，移动到设备源的根目录并运行`extract-files.sh`，如下所示：

```
./extract-files.sh ~/android/system_dump/
```

这将告诉`extract-files.sh`从已挂载的系统转储而不是从连接的设备获取文件。

提取所有专有文件后，请卸载vendor转储（如果之前已挂载）：

```
sudo umount ~/android/system_dump/system/vendor
```

然后卸载系统转储：

```
sudo umount ~/android/system_dump/system

```

最后，在删除不再需要的文件之前卸载任何其他镜像：

```
rm -rf ~/android/system_dump/
```

## 从文件形式的OTA包中提取专有二进制blob文件

新建一个临时目录然后将工作目录移动到那里：

```
mkdir ~/android/system_dump/
cd ~/android/system_dump/
```

从zip包中解压`system`文件夹：
```
unzip path/to/exthm-*.zip system/*
```
`path/to/`是可安装zip包的路径

在你解压`system`文件夹之后，将这个文件夹移动到你的设备的源码的根目录，然后运行`extract-files.sh`，如下所示：

```
./extract-files.sh ~/android/system_dump/
```
这将告诉`extract-files.sh`从已解压的系统转储而不是从连接的设备获取文件。.

提取所有专有文件后，可以删除从zip中提取的文件：

```
rm -rf ~/android/system_dump/
```

## 从堆栈形式的OTA包中提取专有二进制blob文件

新建一个临时目录然后将工作目录移动到那里：

```
mkdir ~/android/system_dump/
cd ~/android/system_dump/
```

从exTHmUI可安装zip文件中提取`payload.bin`文件： 

```
unzip /path/to/exthm-*.zip payload.bin
```

`path/to/`是可安装zip包的路径。

You will now need to use a tool called `update-payload-extractor`.

To use the tool, you will need python-protobuf, if you do not already have it:

```
sudo apt-get install python-protobuf
```

You can now extract the `.img` files from the payload:

* If you have a exTHmUI build tree checked out already, you can just run the script to extract the payload:
  ```
  python /path/to/lineage-tree/lineage/scripts/update-payload-extractor/extract.py payload.bin --output_dir ./
  ```

* If you don't have a exTHmUI build tree checked out, you can clone our scripts repo, and then run the script to extract the payload:
  ```
  git clone https://github.com/LineageOS/scripts
  python /path/to/scripts/update-payload-extractor/extract.py payload.bin --output_dir ./
  ```

It will take a few moments. Once it's done, we will need to mount the `system.img` file, and the `vendor.img` and `product.img` files if they exist, to obtain the complete set of proprietary blobs:

```
mkdir system/
sudo mount system.img system/
sudo mount vendor.img system/vendor/
sudo mount product.img system/product/
```

Move to the root directory of the sources of your device and run `extract-files.sh` as follows:

```
./extract-files.sh ~/android/system_dump/
```

This will tell `extract-files.sh` to extract the proprietary blobs from the mounted system dump rather than a connected device.

Once it is done, unmount the system dump and remove the now unnecessary files:

```
sudo umount -R ~/android/system_dump/system/
rm -rf ~/android/system_dump/
```
