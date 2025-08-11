## **问题1：**上电自动配置固定ip

使用systemd 接管网络服务

[(34条消息) ubuntu开机启动网络配置 - systemd-networkd或netplan_就是个linux工程师的博客-CSDN博客_networkd ubuntu](https://blog.csdn.net/yanceylu/article/details/115284473)

```bash
systemctl enable systemd-networkd
```

启动该服务后，该服务会自动调用对应目录下的network文件

```bash
vi /etc/systemd/network/10-static-eth0.network
```

```bash
[Match]
Name=eth0

[Network]
Address=192.168.4.222/24
DNS=8.8.8.8
Gateway=192.168.4.1
```

.network文件也可以放在其它位置

- /usr/lib/systemd/network 系统网络目录 低优先
- /run/systemd/network 运行时网络目录 中优先

- /etc/systemd/network 本机网络目录 高优先



## 问题2：千兆网有问题，上电后需要配置为百兆网

由于千兆网对交换机、网线有要求，且软件上要做tx/rx延时比较繁琐，结合实际对网络速率需求不高，手动将phy调整为百兆模式

调整之前，因为自己修改phy芯片寄存器比较麻烦，安装ethtool配置更为简便

```bash
apt install ethtool
```

安装完成后，先编写systemd服务

```bash
# /lib/systemd/system/systemd-networkd-after.service


[Unit]
Description=Systemd-Networkd After Service(by Zihao Hu, AWP Tech)
After=systemd-networkd.service
Requires=systemd-networkd.service

[Service]
Type=oneshot
ExecStart=/opt/tools/scripts/systemd-networkd-after.sh
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

这里的服务，将在启动时执行我们的自定义脚本**systemd-networkd-after.sh**，这里用到了ethtool配置eth0为百兆网自协商全双工

```bash
# /opt/tools/scripts/systemd-networkd-after.sh

#!/bin/bash

ethtool -s eth0 speed 100 duplex full autoneg on
```

配置写好后，启动服务

```bash
chmod a+x /opt/tools/scripts/systemd-networkd-after.sh
systemctl daemon-reload
systemctl enable systemd-networkd-after
```

重启设备，查看服务状态

```bash
systemctl status systemd-networkd-after
```

状态如果正常，输出如下

```bash
● systemd-networkd-after.service - Systemd-Networkd After Service(by Zihao Hu, AWP Tech)
   Loaded: loaded (/lib/systemd/system/systemd-networkd-after.service; enabled; vendor preset: enabled)
   Active: active (exited) since Fri 2025-08-08 07:57:13 UTC; 6min ago
  Process: 170 ExecStart=/opt/tools/scripts/systemd-networkd-after.sh (code=exited, status=0/SUCCESS)
 Main PID: 170 (code=exited, status=0/SUCCESS)

Aug 08 07:57:12 localhost.localdomain systemd[1]: Starting Systemd-Networkd After Service(by Zihao Hu, AWP Tech)...
Aug 08 07:57:13 localhost.localdomain systemd[1]: Started Systemd-Networkd After Service(by Zihao Hu, AWP Tech).
```

## 问题3：开机后只能用普通用户登录串口

未解决

## 问题4：docker启动前需要挂载tf卡到文件系统

先了解指令lsblk

```bash
lsblk && lsblk -f
```

```bash
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
mmcblk0      179:0    0  3.7G  0 disk
|-mmcblk0p1  179:1    0    4M  0 part
|-mmcblk0p2  179:2    0    4M  0 part
|-mmcblk0p3  179:3    0   32M  0 part
|-mmcblk0p4  179:4    0   32M  0 part
|-mmcblk0p5  179:5    0  3.4G  0 part /
|-mmcblk0p6  179:6    0  128M  0 part
`-mmcblk0p7  179:7    0   20M  0 part
mmcblk0boot0 179:32   0    4M  1 disk
mmcblk0boot1 179:64   0    4M  1 disk
mmcblk1      179:96   0   58G  0 disk
|-mmcblk1p1  179:97   0    1M  0 part
|-mmcblk1p2  179:98   0 57.7G  0 part
`-mmcblk1p3  179:99   0   50M  0 part
NAME         FSTYPE LABEL       UUID                                 MOUNTPOINT
mmcblk0
|-mmcblk0p1
|-mmcblk0p2
|-mmcblk0p3
|-mmcblk0p4
|-mmcblk0p5  ext4               c29cefb5-a2b7-4c7e-b920-36db6afe21e1 /
|-mmcblk0p6  ext2               a9b54afa-c642-496a-aba3-b6b1bc7f2228
`-mmcblk0p7  ext2               6a00c367-0206-4905-bce5-62524800996e
mmcblk0boot0
mmcblk0boot1
mmcblk1
|-mmcblk1p1  ext4   reserved_fs 2c296828-0867-4bc7-9256-e4552dfdb556
|-mmcblk1p2  ext4   root_fs     8d3153f8-0423-4486-9015-5dbb5f89a7cc
`-mmcblk1p3  vfat   exchange    B578-AF9D
```

如果tf卡不是ext4格式，需要格式化，否则忽略下面这个步骤

```bash
mkfs.ext4 /dev/mmcblk1
```

格式化后如下

```bash
NAME         MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
mmcblk0      179:0    0   7.3G  0 disk 
|-mmcblk0p1  179:1    0     4M  0 part 
|-mmcblk0p2  179:2    0     4M  0 part 
|-mmcblk0p3  179:3    0    32M  0 part 
|-mmcblk0p4  179:4    0    32M  0 part 
|-mmcblk0p5  179:5    0   6.9G  0 part /
|-mmcblk0p6  179:6    0   128M  0 part 
`-mmcblk0p7  179:7    0   248M  0 part 
mmcblk0boot0 179:32   0     8M  1 disk 
mmcblk0boot1 179:64   0     8M  1 disk 
mmcblk1      179:96   0 116.1G  0 disk /mnt/sdcard
NAME         FSTYPE LABEL UUID                                 MOUNTPOINT
mmcblk0                                                        
|-mmcblk0p1                                                    
|-mmcblk0p2                                                    
|-mmcblk0p3                                                    
|-mmcblk0p4                                                    
|-mmcblk0p5  ext4         c29cefb5-a2b7-4c7e-b920-36db6afe21e1 /
|-mmcblk0p6  ext2         a9b54afa-c642-496a-aba3-b6b1bc7f2228 
`-mmcblk0p7  ext2         6a00c367-0206-4905-bce5-62524800996e 
mmcblk0boot0                                                   
mmcblk0boot1                                                   
mmcblk1      ext4         a99f812e-fad1-4aae-9689-da16c899bcb7 /mnt/sdcard
```

可知116.1G的/dev/mmcblk1为tf卡，其uuid为a99f812e-fad1-4aae-9689-da16c899bcb7

于是编写mnt-sdcard.mount如下

```bash
#/lib/systemd/system/mnt-sdcard.mount

[Unit]
Description=Monut sdcard to /mnt/sdcard(by Zihao Hu, AWP Tech)

[Mount]
What=/dev/mmcblk1
Where=/mnt/sdcard
Type=ext4
Options=defaults

[Install]
WantedBy=multi-user.target
```

为什么要叫mnt-sdcard.mount？因为systemd限制了Unit名称必须是挂载位置，比如我们的挂载位置Where是/mnt/sdcard，那么Unit名称必须是mnt-sdcard，用-表示上下级目录的关系

配置写好后，启动服务

```bash
systemctl enable mnt-sdcard.mount
mkdir /mnt/sdcard
```

同时还要在docker.mount中添加对mnt-sdcard.mount的依赖，确保先挂载tf卡再启动docker

这是原来的

```bash
#/lib/systemd/system/docker.mount

[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target docker.socket firewalld.service containerd.service time-set.target
Wants=network-online.target containerd.service
Requires=docker.socket

# ...
```

这是修改后的

```bash
#/lib/systemd/system/docker.mount

[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target docker.socket firewalld.service containerd.service time-set.target mnt-sdcard.mount
Wants=network-online.target containerd.service
Requires=docker.socket mnt-sdcard.mount

# ...
```

