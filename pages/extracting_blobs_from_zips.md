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

If you do not have a split OTA file, you may skip any step that references `vendor.transfer.list` and `vendor.new.dat.br` or `vendor.new.dat`

Create a temporary directory and move there:

```
mkdir ~/android/system_dump/
cd ~/android/system_dump/
```

Extract `system.transfer.list` and `system.new.dat.br` or `system.new.dat` from the installable exTHmUI zip:

```
unzip path/to/lineage-*.zip system.transfer.list system.new.dat*
```
where `path/to/` is the path to the installable zip.

If your OTA includes `vendor.transfer.list` and `vendor.new.dat.br` or `vendor.new.dat` (other others), extract them from the installable exTHmUI zip as well:

```
unzip path/to/lineage-*.zip vendor.transfer.list vendor.new.dat*
```
where `path/to/` is the path to the installable zip.

In the case of `system.new.dat.br`/`vendor.new.dat.br`/etc. (a [brotli](https://en.wikipedia.org/wiki/Brotli) archive) exists, you will first need to decompress them using the `brotli` utility:

```
sudo apt-get install brotli
brotli --decompress --output=system.new.dat system.new.dat.br
```

And if you have a `vendor.dat.new.br` (or others) file:

```
brotli --decompress --output=vendor.new.dat vendor.new.dat.br
```

You now need to get a copy of `sdat2img`. This script can convert the content of block-based OTAs into dumps that can be mounted. `sdat2img` is available at the following git repository that you can clone with:

```
git clone https://github.com/xpirt/sdat2img
```

Once you have obtained `sdat2img`, use it to extract the system image:

```
python sdat2img/sdat2img.py system.transfer.list system.new.dat system.img
```

And if you have a `vendor.dat.new` (or others) file:

```
python sdat2img/sdat2img.py vendor.transfer.list vendor.new.dat vendor.img
```

You should now have a file named `system.img` that you can mount as follows:

```
mkdir system/
sudo mount system.img system/
```

If you also have a file named `vendor.img`, you can mount it as follows:

```
sudo rm system/vendor
sudo mkdir system/vendor
sudo mount vendor.img system/vendor/
```

You must also now mount any other image files that you have in their respective directories.

After you have mounted the image(s), move to the root directory of the sources of your device and run `extract-files.sh` as follows:

```
./extract-files.sh ~/android/system_dump/
```

This will tell `extract-files.sh` to get the files from the mounted system dump rather than from a connected device.

Once you've extracted all the proprietary files, unmount the vendor dump if you mounted it earlier:

```
sudo umount ~/android/system_dump/system/vendor
```

Then unmount the system dump:

```
sudo umount ~/android/system_dump/system

```

Finally, unmount any other images before deleting the no longer needed files:

```
rm -rf ~/android/system_dump/
```

## 从文件形式的OTA包中提取专有二进制blob文件

Create a temporary directory to extract the content of the zip and move there:

```
mkdir ~/android/system_dump/
cd ~/android/system_dump/
```

Extract the `system` folder from the zip:

```
unzip path/to/lineage-*.zip system/*
```
where `path/to/` is the path to the installable zip.

After you have extracted the `system` folder, move to the root directory of the sources of your device and run `extract-files.sh` as follows:

```
./extract-files.sh ~/android/system_dump/
```
This will tell `extract-files.sh` to get the files from the extracted system dump rather than from a connected device.

Once you've extracted all the proprietary files, you can delete the files that were extracted from the zip:

```
rm -rf ~/android/system_dump/
```

## 从堆栈形式的OTA包中提取专有二进制blob文件

Create a temporary directory to extract the content of the zip and move there:

```
mkdir ~/android/system_dump/
cd ~/android/system_dump/
```

Extract the `payload.bin` file from the exTHmUI installation zip file:

```
unzip /path/to/lineage-*.zip payload.bin
```
where `/path/to/` is the path to the installable zip.

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
