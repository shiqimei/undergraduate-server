# 其它笔记

## Hackintosh 安装教程
1. 使用 Gparted 分区，磁盘分区表采用 GPT 格式，先分一个 100M FAT32 格式的 EFI 分区，再分一个 10 G 的 HFS+ 分区，用于存放的 Hackintosh 的安装镜像。

````bash
➜ sudo fdisk -l

Disk /dev/sda: 29.3 GiB, 31466717184 bytes, 61458432 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 873A9E12-9354-4E5B-ABDA-7D7E7FA245E9

Device      Start      End  Sectors  Size Type
/dev/sda1    2048   206847   204800  100M Microsoft basic data
/dev/sda2  206848 20686847 20480000  9.8G Apple HFS/HFS+
````