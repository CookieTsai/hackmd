---
title: Day 20 - GCP 永久硬碟管理 (下)
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : GCP 永久硬碟管理 (下) - Day 20 -

![Imgur](https://i.imgur.com/bG1yt2G.jpg)

## 前言

了解硬碟的大小與效能的關係，從而選擇後在服務運行過程總難免發生硬碟滿了炸了。這時就需要增加硬碟空間，在 GCP 新增硬碟空間是相當於開了一個未格式化的遠端硬碟空間，加載在現有的計算實例中，所以完整的新增硬碟需要 `新增硬碟` > `硬碟連接至計算實例` > `格式化硬碟` > `掛載硬碟` >  `建立鏈結`，接著我們以實際例子來示範。

## 練習範例

過程會新增一台計算實例，我們假定根目錄下的 data 資料夾已快滿，需擴充空間為目標，來做一個實際可能發生的練習。

## 新增硬碟並與計算實例連接

**新增計算實例**

```bash
$ gcloud compute instances create vm-0
```

```bash
Created [https://www.googleapis.com/compute/v1/projects/cookie-first-project/zones/asia-east1-a/instances/vm-0].
NAME  ZONE          MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
vm-0  asia-east1-a  n1-standard-1               10.140.0.11  104.155.198.35  RUNNING
```

**新增永久硬碟**

```bash
$ gcloud compute disks create disk-1 --size 200 --type pd-standard --zone asia-east1-a
```

```bash
Created [https://www.googleapis.com/compute/v1/projects/cookie-first-project/zones/asia-east1-a/disks/disk-1].
NAME    ZONE          SIZE_GB  TYPE         STATUS
disk-1  asia-east1-a  200      pd-standard  READY

New disks are unformatted. You must format and mount a disk before it
can be used. You can find instructions on how to do this at:

https://cloud.google.com/compute/docs/disks/add-persistent-disk#formatting
```

其中：

* `disk-1` 是新磁碟的名稱。
* `200` 是新磁碟以 GB 為單位的大小。
* `pd-standard` 是永久磁碟的類型，可以是 pd-standard 或 pd-ssd。
* `asia-east1-a` 是區域，應當與要掛載的計算實例同一區域

**計算實例連接至硬碟**

```bash
$ gcloud compute instances attach-disk vm-0 --disk disk-1
```

```bash
Updated [https://www.googleapis.com/compute/v1/projects/cookie-first-project/zones/asia-east1-a/instances/vm-0].
```

## 格式化並掛載硬碟

通過『新增硬碟範例』成功為 vm-0 新增了一顆未格式化的硬碟後，要遠端連線進入到 vm-0 這台設備做後續的動作與行為。

**連線至計算實例**

```bash
$ gcloud compute ssh cookie@vm-0
```

**查看硬碟設備資訊**

```bash
$ sudo lsblk
```

```bash
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   10G  0 disk
└─sda1   8:1    0   10G  0 part /
sdb      8:16   0  200G  0 disk
```

備註： `sdb` 就是後來加入的硬碟大小剛好 200G

**格式化硬碟**

> 格式化磁碟。您可使用需要的任何檔案格式，但是最簡單的方法是格式化具有單一 ext4 檔案系統，而且沒有磁碟分區表的整個磁碟。日後如果調整區域永久磁碟的大小，就能調整檔案系統的大小，而不需要修改磁碟分區。
>
> 以 mkfs 工具格式化磁碟。此指令會刪除指定磁碟上的所有資料，所以請確認您正確指定磁碟裝置。為獲得最高的磁碟效能，請在 -E 標記中使用建議的格式化選項。不需要為此次要磁碟上的根目錄保留空間，所以指定 -m 0 以使用全部的可用磁碟空間。

```bash
$ sudo mkfs.ext4 -m 0 -F -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/sdb
```

```bash
mke2fs 1.43.4 (31-Jan-2017)
Discarding device blocks: done
Creating filesystem with 52428800 4k blocks and 13107200 inodes
Filesystem UUID: be75c466-d4b3-46a1-b707-2be4c4f921e4
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872

Allocating group tables: done
Writing inode tables: done
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done
```

重要：`/dev/sdb` 是要格式化的區域永久磁碟裝置 ID 需指定正確

**建立要與裝置連結的資料夾**

```bash
$ sudo mkdir -p /mnt/disks/disk-1
```

**使用 mount 工具將兩者連接**

```bash
$ sudo mount -o discard,defaults /dev/sdb /mnt/disks/disk-1
```

**使用 df 查看剩餘空間**

```bash
$ df
```

```bash
Filesystem     1K-blocks    Used Available Use% Mounted on
udev             1885352       0   1885352   0% /dev
tmpfs             379300    6524    372776   2% /run
/dev/sda1       10253588 1135504   8577516  12% /
tmpfs            1896488       0   1896488   0% /dev/shm
tmpfs               5120       0      5120   0% /run/lock
tmpfs            1896488       0   1896488   0% /sys/fs/cgroup
/dev/sdb       205375464   61468 205297612   1% /mnt/disks/disk-1
```

**替換擴增空間的目錄**

> 執行前先使用 `sudo mkdir /data`，建立一個假設已快滿的 data 資料夾

```bash
$ sudo mv /data /mnt/disks/disk-1
$ sudo ln -s /mnt/disks/disk-1/data /data
```

將資料夾搬移到新的硬碟後，建立軟連結將原本位置的資料夾指向到新的硬碟位置，如此一來就完成空間的擴充咯!!! (灑花)(灑花)(灑花)

## 參考

* [新增或調整區域永久磁碟大小](https://cloud.google.com/compute/docs/disks/add-persistent-disk?hl=zh-tw)
