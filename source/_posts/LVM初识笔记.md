---
title: LVM初识笔记
date: 2022-03-08 20:38:22
tags:
- Linux
---

本文转载自：[https://linux.cn/article-3218-1.html](https://linux.cn/article-3218-1.html)

![](/images/lvm.png)

<!-- more -->

### 准备磁盘设备
使用fdisk命令来准备磁盘设备

### 准备物理券（PV）
使用 pvcreate, pvdisplay, pvremove 命令，来创建、查看和删除物理券

### 准备券组（VG）
使用 vgcreate, vgdisplay, vgremove 命令，来创建、查看和删除券组

### 创建逻辑券（LV）
使用 lvcreate, lvdisplay, lvremove 命令，来创建、查看和删除券组

```bash
# ===============示例==================

# 创建一个名为lv1，大小为100M的逻辑券
lvcreate -L 100M -n lv1 volume-group1

lvdisplay
#  --- Logical volume ---
#  LV Name                /dev/volume-group1/lv1
#  VG Name                volume-group1
#  LV UUID                YNQ1aa-QVt1-hEj6-ArJX-I1Q4-y1h1-OFEtlW
#  LV Write Access        read/write
#  LV Status              available
#  # open                 0
#  LV Size                100.00 MiB
#  Current LE             25
#  Segments               1
#  Allocation             inherit
#  Read ahead sectors     auto
#  - currently set to     256
#  Block device           253:2

# 挂载一个逻辑券
mkfs.ext4 /dev/volume-group1/lv1
mkdir /lvm-mount
mount /dev/volume-group1/lv1 /lvm-mount/

# 卸载一个逻辑券，并删除
umount /lvm-mount/
lvremove /dev/volume-group1/lv1
```
哈哈哈
