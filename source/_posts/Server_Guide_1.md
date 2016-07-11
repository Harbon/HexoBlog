---
  title: 对 Linux 服务器硬盘扩容
  tags: notes
  categories:
  - 服务器
---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最近在开发中遇到因为服务器硬盘满了，需要对硬盘进行扩容的需求，因为用的是青云的 ECS ,所以就以青云为例，记录下如何为服务器硬盘进行扩容（由于没找到如何对特定硬盘升级容量操作，所以搞了一个新的更大的硬盘）。

##### 1.创建和挂载硬盘
首先在青云的后台管理界面创建一块硬盘，具体参数根据需要而定。
![创建硬盘](/images/create_disk.png)
创建好硬盘后，勾选中新建的硬盘，然后点击更多操作，选择加载硬盘到主机。
![加载硬盘到主机](/images/mount_disk.png)
##### 2.对硬盘进行分区，格式化，mount
新的硬盘是没有分区的，所以首先要做的就是对硬盘进行分区。用如下命令查看
```
fdisk -l
```
![](/images/console_shotcut_1.png)
这个命令会显示所有硬盘的信息，可以看到 "Disk /dev/sdc" 下面显示 "Disk /dev/sdc doesn't contain a valid partition table" 表明当前硬盘未进行分区。
接着运行分区命令：
```
fdisk /dev/sdc
```
**/dev/sdc** 是硬盘盘符，然后根据提示，依次输入 n, p, 1, 以及 两次回车，然后是 wq，完成保存。 这样再次通过 fdisk -l 查看时，你可以看到新建的分区 /dev/sdc1
![](/images/console_shotcut_2.png)
分区好后还需要对硬盘进行格式化操作
```
mkfs -t ext4 /dev/sdc1
```
最后是挂载硬盘：
```
mount -t ext4 /dev/sdc1 /mount_dir
```
运行后你会发现系统会报错，原因是你没把 /mount_dir 以及硬盘信息添加到 /etc/fstab 文件
运行 vim 命令后你会发现这个文件是这样的

>/etc/fstab: static file system information.
UUID=5c51f0c7-6ad1-41e5-8026-a75466a07617 /               ext4    errors=remount-ro 0       1
UUID=553cdacd-cc3d-4409-99e5-4d6ab5db975f /var/lib/docker ext4 defaults 0 2
LABEL=YUNIFYSWAP none            swap    sw              0       0
UUID=8ae65141-3051-48a5-826d-1e1ddc3ca70a /home           ext4 defaults 0 2

我们只需要关心前 3 个字段，第一个是UUID是你的硬盘的唯一标识，可通过
```
blkid /dev/sdc1
```
命令来获取，第二个字段是你要挂载的目录绝对路径，第三个是你分区的格式化格式, 后面 3 参数一般就填 defaults 0 2。
添加好后再运行之前的挂载命令就可以了。
