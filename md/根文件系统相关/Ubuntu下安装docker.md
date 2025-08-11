

*由于Ubuntu版本对内核依赖关系，SDK 内核4.19，推荐使用Ubuntu18.04，请先阅读【Ubuntu-镜像搭建】后再查看本篇*

昇腾移植的容器如下，请先安装apt install curl gnupg，

```bash
1.执行switch-to-aliyun-apt.sh先切换阿里云的源（Ubuntu18.04版本专用）

2.chmod -R 777 把整个目录全部改权限

3.先运行install_env.sh安装docker环境

4.运行docker_load_all.sh读取镜像

5.start_env.sh启动或者stop_env.sh停止
```



1.由于原有的昇腾板用的是ubuntu22，所以一开始也是移植22，遇到下面问题

由于docker依赖iptables实现端口转发，而iptables又依赖内核的tcp/ip，在ubuntu22下运行docker启动相关容器时，systemctrl status docker可能会看到

iptables的相关报错：

```bash
iptables v1.8.2 (legacy): can't initialize iptables table `xxx': Table does not exist (do you need to insmod?)
iptables v1.8.4 (legacy): can't initialize iptables table `xxx': Table does not exist (do you need to insmod?)
Perhaps iptables or your kernel needs to be upgraded.
```

需要用update-alternatives --config iptables更改规则，选择nft的手动模式

[在 Ubuntu 22.04 中继续使用 iptables | 伪斜杠青年](https://i.lckiss.com/?p=8770)

但iptables依然会报错

[解决iptables不能使用问题(rk3568/rk3588 5.10.xxx)_can't initialize iptables table `filter': table do-CSDN博客](https://blog.csdn.net/m0_55517777/article/details/136747994)

需要在原有内核基础上打补丁：

```bash
+CONFIG_BRIDGE_NETFILTER=y
+CONFIG_NF_CONNTRACK=y
+CONFIG_NETFILTER_XT_TARGET_CHECKSUM=y
+CONFIG_NETFILTER_XT_TARGET_CLASSIFY=y
+CONFIG_NETFILTER_XT_TARGET_CONNMARK=y
+CONFIG_NETFILTER_XT_TARGET_DSCP=y
+CONFIG_NETFILTER_XT_TARGET_HL=y
+CONFIG_NETFILTER_XT_TARGET_HMARK=y
+CONFIG_NETFILTER_XT_TARGET_IDLETIMER=y
+CONFIG_NETFILTER_XT_TARGET_LOG=y
+CONFIG_NETFILTER_XT_TARGET_MARK=y
+CONFIG_NETFILTER_XT_TARGET_NETMAP=y
+CONFIG_NETFILTER_XT_TARGET_NFLOG=y
+CONFIG_NETFILTER_XT_TARGET_NFQUEUE=y
+CONFIG_NETFILTER_XT_TARGET_REDIRECT=y
+CONFIG_NETFILTER_XT_TARGET_TEE=y
+CONFIG_NETFILTER_XT_TARGET_TPROXY=y
+CONFIG_NETFILTER_XT_TARGET_TCPMSS=y
+CONFIG_NETFILTER_XT_TARGET_TCPOPTSTRIP=y
+CONFIG_NETFILTER_XT_MATCH_ADDRTYPE=y
+CONFIG_NETFILTER_XT_MATCH_BPF=y
+CONFIG_NETFILTER_XT_MATCH_CGROUP=y
+CONFIG_NETFILTER_XT_MATCH_CLUSTER=y
+CONFIG_NETFILTER_XT_MATCH_COMMENT=y
+CONFIG_NETFILTER_XT_MATCH_CONNBYTES=y
+CONFIG_NETFILTER_XT_MATCH_CONNLABEL=y
+CONFIG_NETFILTER_XT_MATCH_CONNLIMIT=y
+CONFIG_NETFILTER_XT_MATCH_CONNMARK=y
+CONFIG_NETFILTER_XT_MATCH_CONNTRACK=y
+CONFIG_NETFILTER_XT_MATCH_CPU=y
+CONFIG_NETFILTER_XT_MATCH_DCCP=y
+CONFIG_NETFILTER_XT_MATCH_DEVGROUP=y
+CONFIG_NETFILTER_XT_MATCH_DSCP=y
+CONFIG_NETFILTER_XT_MATCH_ECN=y
+CONFIG_NETFILTER_XT_MATCH_ESP=y
+CONFIG_NETFILTER_XT_MATCH_HASHLIMIT=y
+CONFIG_NETFILTER_XT_MATCH_HELPER=y
+CONFIG_NETFILTER_XT_MATCH_HL=y
+CONFIG_NETFILTER_XT_MATCH_IPCOMP=y
+CONFIG_NETFILTER_XT_MATCH_IPRANGE=y
+CONFIG_NETFILTER_XT_MATCH_IPVS=y
+CONFIG_NETFILTER_XT_MATCH_L2TP=y
+CONFIG_NETFILTER_XT_MATCH_LENGTH=y
+CONFIG_NETFILTER_XT_MATCH_LIMIT=y
+CONFIG_NETFILTER_XT_MATCH_MAC=y
+CONFIG_NETFILTER_XT_MATCH_MARK=y
+CONFIG_NETFILTER_XT_MATCH_MULTIPORT=y
+CONFIG_NETFILTER_XT_MATCH_NFACCT=y
+CONFIG_NETFILTER_XT_MATCH_OSF=y
+CONFIG_NETFILTER_XT_MATCH_OWNER=y
+CONFIG_NETFILTER_XT_MATCH_POLICY=y
+CONFIG_NETFILTER_XT_MATCH_PHYSDEV=y
+CONFIG_NETFILTER_XT_MATCH_PKTTYPE=y
+CONFIG_NETFILTER_XT_MATCH_QUOTA=y
+CONFIG_NETFILTER_XT_MATCH_QUOTA2=y
+CONFIG_NETFILTER_XT_MATCH_QUOTA2_LOG=y
+CONFIG_NETFILTER_XT_MATCH_RATEEST=y
+CONFIG_NETFILTER_XT_MATCH_REALM=y
+CONFIG_NETFILTER_XT_MATCH_RECENT=y
+CONFIG_NETFILTER_XT_MATCH_SCTP=y
+CONFIG_NETFILTER_XT_MATCH_SOCKET=y
+CONFIG_NETFILTER_XT_MATCH_STATE=y
+CONFIG_NETFILTER_XT_MATCH_STATISTIC=y
+CONFIG_NETFILTER_XT_MATCH_STRING=y
+CONFIG_NETFILTER_XT_MATCH_TCPMSS=y
+CONFIG_NETFILTER_XT_MATCH_TIME=y
+CONFIG_NETFILTER_XT_MATCH_U32=y
+CONFIG_IP_VS=y
+CONFIG_IP_NF_FILTER=y
+CONFIG_IP_NF_NAT=y
+CONFIG_IP_NF_TARGET_MASQUERADE=y
+CONFIG_IP6_NF_IPTABLES=y
+CONFIG_IP6_NF_MANGLE=y
+CONFIG_IP6_NF_NAT=y
+CONFIG_IP6_NF_TARGET_MASQUERADE=y
+CONFIG_BRIDGE=y
+CONFIG_VLAN_8021Q=y
+CONFIG_NETLINK_DIAG=y
```

解决以上问题后，启动docker的过程中还会报pipe error，排除内存问题后依然出现，决定先放弃ubuntu22版本

2.更换为ubuntu18版本后，请注意要更改容器的安装源，将ubuntu代号改为bionic

在安装docker的过程中，会报找不到docker-model-plugin这种包，这是因为18版本没有这个包，

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
```

而网上下载的get-docker.sh是基于最新的ubuntu，会去安装18版本这个不存在的包，

所以请手动安装相关docker组件

[get-docker.sh 和手动添加源仓库安装 Docker 各有优劣 - 皇帽讲绿帽带法技巧 - 博客园](https://www.cnblogs.com/autopwn/p/18706526)

```bash
apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

启动过程中报以下错误：

```bash
docker: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "process_linux.go:449: container init caused \"rootfs_linux.go:58: mounting \\\"mqueue\\\" to rootfs \\\"/var/lib/docker/vfs/dir/a8ba4cb8ee4d172961ab433e41af1725c1aab76fc9123d4908303022e70d4230\\\" at \\\"/dev/mqueue\\\" caused \\\"no such device\\\"\"": unknown
```

这是因为没有/dev/mqueue模块，添加POSIX_MQUEUE即可

[docker-error-at \\\“/dev/mqueue\\\“ caused \\\“no such device_docker no such device-CSDN博客](https://blog.csdn.net/u012587637/article/details/108005750)

至此，安装完成，内存占用如下：

```
root@localhost:~# free -m
              total        used        free      shared  buff/cache   available
Mem:           3900         607        2318          30         974        3222
Swap:             0           0           0
root@localhost:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        14G   12G  897M  94% /
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           2.0G     0  2.0G   0% /dev/shm
tmpfs           2.0G  856K  2.0G   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           2.0G     0  2.0G   0% /sys/fs/cgroup
tmpfs           391M     0  391M   0% /run/user/999
tmpfs           391M     0  391M   0% /run/user/0
root@localhost:~# docker ps
CONTAINER ID   IMAGE                                 COMMAND                  CREATED        STATUS             PORTS                                                                                                                                                 NAMES
5ce43d41f610   mcr.microsoft.com/dotnet/aspnet:3.1   "/bin/sh -c 'sleep 1…"   18 hours ago   Up About an hour   0.0.0.0:8099->8099/tcp, :::8099->8099/tcp                                                                                                             dotnet-3.1-webapi
f5e4e64f2275   mcr.microsoft.com/dotnet/aspnet:3.1   "/bin/sh -c 'sleep 1…"   18 hours ago   Up About an hour   0.0.0.0:9990->9990/tcp, :::9990->9990/tcp                                                                                                             dotnet-3.1-deviceserver
24392149c26c   rabbitmq:3-management                 "docker-entrypoint.s…"   18 hours ago   Up About an hour   4369/tcp, 5671/tcp, 0.0.0.0:5672->5672/tcp, :::5672->5672/tcp, 15671/tcp, 15691-15692/tcp, 25672/tcp, 0.0.0.0:15672->15672/tcp, :::15672->15672/tcp   rabbitmq
8034aaef963a   biarms/mysql:5.7                      "/usr/local/bin/dock…"   18 hours ago   Up About an hour   0.0.0.0:3306->3306/tcp, :::3306->3306/tcp                                                                                                             mysql57
01f12f30819e   nginx                                 "/docker-entrypoint.…"   18 hours ago   Up About an hour   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp                                                                              nginx
2ea7819f143c   redis:latest                          "docker-entrypoint.s…"   18 hours ago   Up About an hour   0.0.0.0:6379->6379/tcp, :::6379->6379/tcp
```
