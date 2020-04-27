# hostIPd地址不正确问题
[TOC]

## 问题

```
[interface@ecs-nxhm-interface-docker-55 ~]$ kubectl -n ecs-ssp get pod -o wide | grep -v cbss99 | grep 103
ecs-ssp.sspro-query-record.cbss-gb8pk                0/1     Running   0          4s    172.128.40.2    ecs-nxhm-ssp-webl-132.46.120.103   <none>           <none>



[interface@ecs-nxhm-interface-docker-55 ~]$ kubectl -n ecs-ssp get pod ecs-ssp.sspro-query-record.cbss-gb8pk -o json
{
    ...
    {
        
        ...
        "hostIP": "132.46.120.103",
        "phase": "Running",
        "podIP": "172.128.40.2",
        "qosClass": "BestEffort",
        "startTime": "2019-03-28T02:52:41Z"
    }
}
```


```
ifconfig
bond23: flags=5187<UP,BROADCAST,RUNNING,MASTER,MULTICAST>  mtu 1500
        inet 132.46.120.103  netmask 255.255.255.0  broadcast 132.46.120.255
        inet6 fe80::f603:43ff:fe4b:ef0c  prefixlen 64  scopeid 0x20<link>
        ether f4:03:43:4b:ef:0c  txqueuelen 1000  (Ethernet)
        RX packets 48015072  bytes 4929601519 (4.5 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 35784734  bytes 2687136702 (2.5 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.128.40.1  netmask 255.255.255.0  broadcast 172.128.40.255
        inet6 fe80::42:6cff:fe08:24a5  prefixlen 64  scopeid 0x20<link>
        ether 02:42:6c:08:24:a5  txqueuelen 0  (Ethernet)
        RX packets 85503  bytes 5661546 (5.3 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 60372  bytes 5029268 (4.7 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        
        ...
        
virbr0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255
        ether 52:54:00:13:98:bb  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```


```
[ailkssp@ecs-nxhm-ssp-webl-132 ~]$ ps -ef|grep flannel
root     15229     1  0 Mar27 ?        00:01:12 /vos/flannel/bin/flanneld -etcd-endpoints=http://etcd.vos.com:2379 -etcd-prefix=/coreos.com/cluster1/network --healthz-port=3399 --ip-masq &
[ailkssp@ecs-nxhm-ssp-webl-132 ~]$ ps -ef|grep dockerd
ailkssp  14794 12481  0 10:30 pts/0    00:00:00 grep --color=auto dockerd
root     16412     1  1 Mar27 ?        00:24:11 /vos/docker/bin/dockerd --bip=172.128.40.1/24 --ip-masq=false --mtu=1500 --config-file=/vos/docker/config/dockerd_config.json
```

```
[ailkssp@ecs-nxhm-ssp-webl-132 ~]$ docker run -it --rm hub.ecs.com:6999/vos-glances:v2.3 bash
root@a00f4391845c:/glances# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
26: eth0@if27: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:80:28:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.128.40.3/24 brd 172.128.40.255 scope global eth0
       valid_lft forever preferred_lft forever
root@a00f4391845c:/glances#
```


## 修复
将问题网卡删除, 对应pod需要由apiserveir重新调度即可修复
```
sudo ifconfig virbr0 down
sudo brctl delbr virbr0
```
