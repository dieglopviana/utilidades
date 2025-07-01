---
layout: default
title: Resize Disk Google Cloud
---

# How to Resize Disk of a VM Instance in Google Cloud

How to Resize Disk of a VM Instance in Google Cloud. In this guide you are going to learn how to resize the disk space of your Compute Engine instance on the fly without any downtime.

You can also [attach and mount new additional disk to your instance instead](https://www.cloudbooklet.com/developer/attach-and-mount-disks-to-vm-instance-in-google-cloud/) of resizing your disk.

### Prerequisites

1. SSH access to your VM Instance.
2. Backup your disk by creating a snapshot of your disk. You can also setup automatic snapshot schedules.
3. Basic SSH terminal skills to execute commands.

Important: You can only enlarge the size of the existing disk, you cannot shrink your disk to lower size.

### Check Disk Size

Before resizing your disk size you can check your available disk space so you will get an idea about the available space in your disk. It is recommended to increase the size if your used space is more than 80%.

```sh
df -h
```

You will get an output similar to the one below.

```bash
Output
Filesystem      Size  Used Avail Use% Mounted on
udev            286M     0  286M   0% /dev
tmpfs            60M  2.4M   57M   4% /run
/dev/sda1       9.8G  1.1G  8.2G  12% /
tmpfs           297M     0  297M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           297M     0  297M   0% /sys/fs/cgroup
```
Here **```/dev/sda1```** is the one which shows the available and used space of your disk. I tested this on a new fresh disk with 10GB space.

### Check Partition

Now you need to check the partitions available on the your disk.

```bash
lsblk
```

This command shows the available partitions. You will get an output similar to this.

```bash
Output
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda      8:0    0  10G  0 disk 
└─sda1   8:1    0  10G  0 part /
```

In this case we having only one partition.

As you can see, **```sda```** is the **```DEVICE_ID```** and **```1```** is the partition number.

### Increase Disk Size

- Go to your Google Cloud Console and navigate to **Compute >> VM Instances**.
- Click the name of the instance where you want to add a disk.
- Scroll down to **Boot disk** and click on the disk name.
- Now you will be taken to the Disk Management page.
- Click **Edit** on the top.
- Here you can specify the size of you need.
- Click **Save** in the bottom to apply the changes.

![image](https://github.com/user-attachments/assets/c0b1801a-b25f-4bb4-8e86-2f635365ca64)

### Grow Partition

Now you need to resize the partition using the **```growpart```** command with your device id and partition number.

```bash
sudo growpart /dev/sda 1
```

You will get something similar.

```bash
Output
CHANGED: partition=1 start=4096 old: size=20967424 end=20971520 new: size=1048571871,end=1048575967
```

### Resize File System

The last step is to resize the filesystem with the **```resize2fs```** command.

```bash
sudo resize2fs /dev/sda1
```

The output will be like this.

```bash
Output
resize2fs 1.43.4 (31-Jan-2017)
Filesystem at /dev/sda1 is mounted on /; on-line resizing required
old_desc_blocks = 2, new_desc_blocks = 63
The filesystem on /dev/sda1 is now 131071483 (4k) blocks long.
```

### Verify the Setup

Now you can verify the disk space using the **```df```** command. Your disk space must have rezised to the additional space you added.

```bash
df -h
```

```bash
Output
Filesystem      Size  Used Avail Use% Mounted on
udev            286M     0  286M   0% /dev
tmpfs            60M  2.4M   57M   4% /run
/dev/sda1       493G  1.2G  471G   1% /
tmpfs           297M     0  297M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           297M     0  297M   0% /sys/fs/cgroup
```

### Conclusion

Now you have learned how to resize your VM Instance without restarting and without downtime.
