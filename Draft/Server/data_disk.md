## Lunix挂载数据盘

本文以CentOS为例介绍如何为Linux系统挂载数据盘

### 查看当前挂载情况

使用`df -Th`可以查看当前挂载情况：

```
Filesystem     Type      Size  Used Avail Use% Mounted on
devtmpfs       devtmpfs  2.0G     0  2.0G   0% /dev
tmpfs          tmpfs     2.0G     0  2.0G   0% /dev/shm
tmpfs          tmpfs     768M  8.9M  759M   2% /run
/dev/vda1      ext4       53G  2.3G   49G   5% /
tmpfs          tmpfs     384M     0  384M   0% /run/user/0
```
上面并没有我们的数据盘，其中/dev/vda1对应的是根目录是我们的系统盘

### 查看硬盘信息

使用`fdisk-l`命令可以查看当前系统硬盘信息

```
Disk /dev/vda: 50 GiB, 53687091200 bytes, 104857600 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x59000bba

Device     Boot Start       End   Sectors Size Id Type
/dev/vda1  *     2048 104857566 104855519  50G 83 Linux


Disk /dev/vdb: 100 GiB, 107374182400 bytes, 209715200 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
[root@VM-0-5-centos ~]# df -TH
Filesystem     Type      Size  Used Avail Use% Mounted on
devtmpfs       devtmpfs  2.0G     0  2.0G   0% /dev
tmpfs          tmpfs     2.0G     0  2.0G   0% /dev/shm
tmpfs          tmpfs     768M  8.9M  759M   2% /run
/dev/vda1      ext4       53G  2.3G   49G   5% /
tmpfs          tmpfs     384M     0  384M   0% /run/user/0
```

可以发现我们100G的数据盘/dev/vdb，但是没有挂载到系统上

### 硬盘分区

使用命令`fdisk /dev/vdb`，可以将我们的硬盘/dev/vdb挂载到系统上。执行命令后进入fdisk模式，输入n命令，接下来的可以一路return键，使用默认方式挂载硬盘。然后执行p命令查看分区信息，最后使用w命令保存。

```
Welcome to fdisk (util-linux 2.37.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x4f798fea.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-209715199, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-209715199, default 209715199): 

Created a new partition 1 of type 'Linux' and of size 100 GiB.

Command (m for help): p
Disk /dev/vdb: 100 GiB, 107374182400 bytes, 209715200 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x4f798fea

Device     Boot Start       End   Sectors  Size Id Type
/dev/vdb1        2048 209715199 209713152  100G 83 Linux

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

然后执行`partprobe`命令，将新的分区表变更同步至操作系统。

### 设定分区文件格式

使用`mkfs -t ext4 /dev/vdb1` 将分区/dev/vdb1设置为ext4格式（和系统盘格式相同）

### 执行挂载

使用`mount /dev/vdb1 /mnt/datadisk`，可以将分区/dev/vdb1挂载到/mnt/datadisk挂载点（即目录，如果没有需要创建）。

再次使用`df -Th`查看挂载情况：

```
Filesystem     Type      Size  Used Avail Use% Mounted on
devtmpfs       devtmpfs  1.8G     0  1.8G   0% /dev
tmpfs          tmpfs     1.8G     0  1.8G   0% /dev/shm
tmpfs          tmpfs     732M  8.5M  724M   2% /run
/dev/vda1      ext4       50G  2.7G   45G   6% /
/dev/vdb1      ext4       98G   24K   93G   1% /mnt/datadisk
tmpfs          tmpfs     366M     0  366M   0% /run/user/0
```

可以看到新的磁盘/dev/vdb1已经成功挂载到/mnt/datadisk。

### 开机自动挂载

挂载好的磁盘需要设置开机自动挂载，推荐使用UUID来配置自动挂载盘：

使用`blkid`可以查看挂载盘的uuid

```
/dev/sr0: BLOCK_SIZE="2048" UUID="2022-08-11-16-16-24-00" LABEL="config-2" TYPE="iso9660"
/dev/vdb1: UUID="5db28770-68ee-45f2-8e11-59a8e0a4efc1" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="4f798fea-01"
/dev/vda1: UUID="ceba5bd8-a35a-4599-9dee-6f3120630969" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="59000bba-01"
```

可以看到/dev/vdb1的uuid是5db28770-68ee-45f2-8e11-59a8e0a4efc1

然后编辑/etc/fstab，添加自动挂载盘

```
$ vim /etc/fstab

# /etc/fstab
# Created by anaconda on Thu Jan 20 07:20:28 2022
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
UUID=ceba5bd8-a35a-4599-9dee-6f3120630969 /                       ext4    defaults        1 1
UUID=5db28770-68ee-45f2-8e11-59a8e0a4efc1 /mnt/datadisk           ext4    defaults        1 1
```

重启后可再次使用`df -Th`查看数据盘是否自动挂载


### 参考

[CentOS7挂载新数据盘的完整步骤](https://cloud.tencent.com/developer/article/1720967?from=15425)