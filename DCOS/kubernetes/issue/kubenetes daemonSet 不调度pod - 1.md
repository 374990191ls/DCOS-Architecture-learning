# kubernetes daemonSet部分时候不调度pod

**【尚未解决】**

[TOC]

## 环境说明
```
[root@host-10 ~]$ kubectl version
Client Version: version.Info{Major:"1", Minor:"6", GitVersion:"v1.6.8", GitCommit:"d74e09bb4e4e7026f45becbed8310665ddcb8514", GitTreeState:"clean", BuildDate:"2017-08-03T18:12:08Z", GoVersion:"go1.7.6", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"6", GitVersion:"v1.6.8", GitCommit:"d74e09bb4e4e7026f45becbed8310665ddcb8514", GitTreeState:"clean", BuildDate:"2017-08-03T18:01:01Z", GoVersion:"go1.7.6", Compiler:"gc", Platform:"linux/amd64"}

[root@host-10 ~]$ docker version
Client:
 Version:      1.12.6
 API version:  1.24
 Go version:   go1.6.4
 Git commit:   78d1802
 Built:        Tue Jan 10 20:20:19 2017
 OS/Arch:      linux/amd64
 Experimental: true

Server:
 Version:      1.12.6
 API version:  1.24
 Go version:   go1.6.4
 Git commit:   78d1802
 Built:        Tue Jan 10 20:20:19 2017
 OS/Arch:      linux/amd64
 Experimental: true
```


## ds 描述信息
```
[uac@ecs-nxhm-pltfrm-docker-11 ~]$ kubectl -n kube-system describe ds kube-proxy
Name:		kube-proxy
Selector:	app=kube-proxy
Node-Selector:	nodeAttr=slave
Labels:		app=kube-proxy
Annotations:	<none>
Desired Number of Nodes Scheduled: 57
Current Number of Nodes Scheduled: 55
Number of Nodes Scheduled with Up-to-date Pods: 55
Number of Nodes Scheduled with Available Pods: 52
Number of Nodes Misscheduled: 0
Pods Status:	54 Running / 1 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:	app=kube-proxy
  Containers:
   kube-proxy:
    Image:	hub.ecs.com:6999/kube-proxy:v1.6
    Port:	<none>
    Command:
      /bin/sh
      -c
      kube-proxy --master=http://kubernetes.vos.com:8080 --log-dir=/var/log --iptables-sync-period=10s
    Environment:	<none>
    Mounts:		<none>
  Volumes:		<none>
Events:
  FirstSeen	LastSeen	Count	From			SubObjectPath	Type		Reason		Message
  ---------	--------	-----	----			-------------	--------	------		-------
  2d		28s		12	daemonset-controller			Normal		FailedPlacement	failed to place pod on "ecs-nxhm-unipay-dmz-14": Node didn't have enough resource: pods, requested: 1, used: 0, capacity: 0
[uac@ecs-nxhm-pltfrm-docker-11 ~]$
```


## node信息
```
[uac@ecs-nxhm-pltfrm-docker-11 ~]$ kubectl get node ecs-nxhm-unipay-dmz-14 -o wide
NAME                     STATUS    AGE       VERSION   EXTERNAL-IP   OS-IMAGE                                      KERNEL-VERSION
ecs-nxhm-unipay-dmz-14   Ready     11m       v1.6.8    <none>        Red Hat Enterprise Linux Server 7.4 (Maipo)   3.10.0-693.el7.x86_64
[uac@ecs-nxhm-pltfrm-docker-11 ~]$ kubectl describe  node ecs-nxhm-unipay-dmz-14
Name:			ecs-nxhm-unipay-dmz-14
Role:
Labels:			domain=cluster-unipay-web
			hostname=ecs-nxhm-unipay-dmz-14
			nodeAttr=slave
Annotations:		node.alpha.kubernetes.io/ttl=0
			volumes.kubernetes.io/controller-managed-attach-detach=true
Taints:			<none>
CreationTimestamp:	Mon, 02 Mar 2020 17:19:52 +0800
Conditions:
  Type			Status	LastHeartbeatTime			LastTransitionTime			Reason				Message
  ----			------	-----------------			------------------			------				-------
  OutOfDisk 		False 	Mon, 02 Mar 2020 17:31:17 +0800 	Mon, 02 Mar 2020 17:19:54 +0800 	KubeletHasSufficientDisk 	kubelet has sufficient disk space available
  MemoryPressure 	False 	Mon, 02 Mar 2020 17:31:17 +0800 	Mon, 02 Mar 2020 17:19:54 +0800 	KubeletHasSufficientMemory 	kubelet has sufficient memory available
  DiskPressure 		False 	Mon, 02 Mar 2020 17:31:17 +0800 	Mon, 02 Mar 2020 17:19:54 +0800 	KubeletHasNoDiskPressure 	kubelet has no disk pressure
  Ready 		True 	Mon, 02 Mar 2020 17:31:17 +0800 	Mon, 02 Mar 2020 17:28:26 +0800 	KubeletReady 			kubelet is posting ready status
Addresses:
  LegacyHostIP:	132.46.32.14
  InternalIP:	132.46.32.14
  Hostname:	ecs-nxhm-unipay-dmz-14
Capacity:
 cpu:		32
 memory:	131484920Ki
 pods:		10k
Allocatable:
 cpu:		32
 memory:	131382520Ki
 pods:		10k
System Info:
 Machine ID:			e7cdbf5f5b4342d4b8c0793c00ac7fc8
 System UUID:			A8BF926C-9A80-03E7-B211-D21D2059C81A
 Boot ID:			842f2a8d-cdc0-437b-91d6-3d7a97b2115e
 Kernel Version:		3.10.0-693.el7.x86_64
 OS Image:			Red Hat Enterprise Linux Server 7.4 (Maipo)
 Operating System:		linux
 Architecture:			amd64
 Container Runtime Version:	docker://1.12.6
 Kubelet Version:		v1.6.8
 Kube-Proxy Version:		v1.6.8
ExternalID:			ecs-nxhm-unipay-dmz-14
Non-terminated Pods:		(1 in total)
  Namespace			Name				CPU Requests	CPU Limits	Memory Requests	Memory Limits
  ---------			----				------------	----------	---------------	-------------
  default			daemonset-glance-zzmc3		0 (0%)		0 (0%)		0 (0%)		0 (0%)
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  CPU Requests	CPU Limits	Memory Requests	Memory Limits
  ------------	----------	---------------	-------------
  0 (0%)	0 (0%)		0 (0%)		0 (0%)
Events:
  FirstSeen	LastSeen	Count	From				SubObjectPath	Type		Reason			Message
  ---------	--------	-----	----				-------------	--------	------			-------
  41m		41m		1	kubelet, ecs-nxhm-unipay-dmz-14			Warning		ImageGCFailed		unable to find data for container /
  41m		41m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		Starting		Starting kubelet.
  41m		41m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeNotReady		Node ecs-nxhm-unipay-dmz-14 status is now: NodeNotReady
  40m		40m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeReady		Node ecs-nxhm-unipay-dmz-14 status is now: NodeReady
  41m		34m		5	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientDisk	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientDisk
  41m		34m		5	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientMemory	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientMemory
  41m		34m		5	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasNoDiskPressure	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasNoDiskPressure
  37m		34m		2	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeNotSchedulable	Node ecs-nxhm-unipay-dmz-14 status is now: NodeNotSchedulable
  36m		33m		2	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeSchedulable		Node ecs-nxhm-unipay-dmz-14 status is now: NodeSchedulable
  27m		27m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeNotReady		Node ecs-nxhm-unipay-dmz-14 status is now: NodeNotReady
  27m		27m		1	kubelet, ecs-nxhm-unipay-dmz-14			Warning		ImageGCFailed		unable to find data for container /
  27m		27m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientDisk	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientDisk
  27m		27m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		Starting		Starting kubelet.
  27m		27m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasNoDiskPressure	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasNoDiskPressure
  27m		27m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientMemory	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientMemory
  27m		27m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeReady		Node ecs-nxhm-unipay-dmz-14 status is now: NodeReady
  23m		23m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		Starting		Starting kubelet.
  23m		23m		1	kubelet, ecs-nxhm-unipay-dmz-14			Warning		ImageGCFailed		unable to find data for container /
  23m		23m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientDisk	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientDisk
  23m		23m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientMemory	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientMemory
  23m		23m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasNoDiskPressure	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasNoDiskPressure
  23m		23m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeNotReady		Node ecs-nxhm-unipay-dmz-14 status is now: NodeNotReady
  23m		23m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeReady		Node ecs-nxhm-unipay-dmz-14 status is now: NodeReady
  21m		21m		1	kubelet, ecs-nxhm-unipay-dmz-14			Warning		ImageGCFailed		unable to find data for container /
  21m		21m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		Starting		Starting kubelet.
  21m		11m		3	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientDisk	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientDisk
  21m		11m		3	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientMemory	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientMemory
  21m		11m		3	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasNoDiskPressure	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasNoDiskPressure
  7m		7m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		Starting		Starting kubelet.
  7m		7m		1	kubelet, ecs-nxhm-unipay-dmz-14			Warning		ImageGCFailed		unable to find data for container /
  7m		7m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientDisk	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientDisk
  7m		7m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientMemory	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientMemory
  7m		7m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasNoDiskPressure	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasNoDiskPressure
  5m		5m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		Starting		Starting kubelet.
  5m		5m		1	kubelet, ecs-nxhm-unipay-dmz-14			Warning		ImageGCFailed		unable to find data for container /
  5m		5m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientMemory	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientMemory
  5m		5m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientDisk	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientDisk
  5m		5m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasNoDiskPressure	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasNoDiskPressure
  5m		5m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeNotReady		Node ecs-nxhm-unipay-dmz-14 status is now: NodeNotReady
  5m		5m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeReady		Node ecs-nxhm-unipay-dmz-14 status is now: NodeReady
  3m		3m		1	kubelet, ecs-nxhm-unipay-dmz-14			Warning		ImageGCFailed		unable to find data for container /
  3m		3m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		Starting		Starting kubelet.
  3m		3m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientDisk	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientDisk
  3m		3m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasSufficientMemory	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasSufficientMemory
  3m		3m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeHasNoDiskPressure	Node ecs-nxhm-unipay-dmz-14 status is now: NodeHasNoDiskPressure
  3m		3m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeNotReady		Node ecs-nxhm-unipay-dmz-14 status is now: NodeNotReady
  3m		3m		1	kubelet, ecs-nxhm-unipay-dmz-14			Normal		NodeReady		Node ecs-nxhm-unipay-dmz-14 status is now: NodeReady
[uac@ecs-nxhm-pltfrm-docker-11 ~]$
```



## 查看kubelet日志信息
```
[root@ecs-nxhm-unipay-dmz-14 logs]# tail -f ./kubelet.INFO
I0302 17:24:05.941820   35352 oomparser.go:185] oomparser using systemd
I0302 17:24:05.942382   35352 manager.go:288] Starting recovery of all containers
I0302 17:24:05.978683   35352 kubelet_node_status.go:128] Node ecs-nxhm-unipay-dmz-14 was previously registered
I0302 17:24:05.978699   35352 kubelet_node_status.go:178] Controller attach-detach setting changed to true; updating existing Node
I0302 17:24:05.993459   35352 manager.go:293] Recovery completed
I0302 17:24:06.070934   35352 kubelet_node_status.go:80] Successfully registered node ecs-nxhm-unipay-dmz-14
I0302 17:24:11.016838   35352 reconciler.go:242] VerifyControllerAttachedVolume operation started for volume "kubernetes.io/host-path/0003d38a-5c67-11ea-861c-30e1716f864c-docker" (spec.Name: "docker") pod "0003d38a-5c67-11ea-861c-30e1716f864c" (UID: "0003d38a-5c67-11ea-861c-30e1716f864c")
I0302 17:24:11.117035   35352 operation_generator.go:597] MountVolume.SetUp succeeded for volume "kubernetes.io/host-path/0003d38a-5c67-11ea-861c-30e1716f864c-docker" (spec.Name: "docker") pod "0003d38a-5c67-11ea-861c-30e1716f864c" (UID: "0003d38a-5c67-11ea-861c-30e1716f864c").
I0302 17:24:57.206531   35352 kuberuntime_manager.go:458] Container {Name:daemonset-glance Image:hub.ecs.com:6999/vos-glances:v2.3 Command:[] Args:[] WorkingDir: Ports:[] EnvFrom:[] Env:[{Name:kafka_location Value:132.46.120.11:9092,132.46.120.12:9092,132.46.126.54:9092 ValueFrom:nil} {Name:GLANCES_OPT Value:-t 10 --disable-amps --disable-alert --disable-cpu --disable-diskio --disable-folders --disable-fs --disable-ip --disable-load --disable-network --disable-now --disable-process --disable-raid --disable-sensors --disable-wifi --disable-irix --disable-left-sidebar --disable-quicklook --disable-top --disable-bg --disable-bold --export-kafka ValueFrom:nil}] Resources:{Limits:map[] Requests:map[]} VolumeMounts:[{Name:docker ReadOnly:false MountPath:/var/run/docker.sock SubPath:}] LivenessProbe:nil ReadinessProbe:nil Lifecycle:nil TerminationMessagePath:/dev/termination-log TerminationMessagePolicy:File ImagePullPolicy:IfNotPresent SecurityContext:nil Stdin:true StdinOnce:false TTY:true} is dead, but RestartPolicy says that we should restart it.
I0302 17:24:57.206582   35352 kuberuntime_manager.go:742] checking backoff for container "daemonset-glance" in pod "daemonset-glance-zzmc3_default(0003d38a-5c67-11ea-861c-30e1716f864c)"
^C
[root@ecs-nxhm-unipay-dmz-14 logs]# tail -f ./kubelet.WARNING
Log line format: [IWEF]mmdd hh:mm:ss.uuuuuu threadid file:line] msg
W0302 17:24:05.238437   35352 server.go:715] Could not load kubeconfig file /var/lib/kubelet/kubeconfig: stat /var/lib/kubelet/kubeconfig: no such file or directory. Using default client config instead.
W0302 17:24:05.240405   35352 cni.go:157] Unable to update cni config: No networks found in /etc/cni/net.d
W0302 17:24:05.407191   35352 manager.go:151] unable to connect to Rkt api service: rkt: cannot tcp Dial rkt api service: dial tcp [::1]:15441: getsockopt: connection refused
W0302 17:24:05.620587   35352 kubelet_network.go:70] Hairpin mode set to "promiscuous-bridge" but kubenet is not enabled, falling back to "hairpin-veth"
W0302 17:24:05.622700   35352 cni.go:157] Unable to update cni config: No networks found in /etc/cni/net.d
E0302 17:24:05.813195   35352 kubelet.go:1165] Image garbage collection failed: unable to find data for container /
E0302 17:24:05.917829   35352 kubelet.go:1661] Failed to check if disk space is available for the runtime: failed to get fs info for "runtime": unable to find data for container /
E0302 17:24:05.917855   35352 kubelet.go:1669] Failed to check if disk space is available on the root partition: failed to get fs info for "root": unable to find data for container /
W0302 17:24:05.940251   35352 manager.go:247] Registration of the rkt container factory failed: unable to communicate with Rkt api service: rkt: cannot tcp Dial rkt api service: dial tcp [::1]:15441: getsockopt: connection refused
^C
[root@ecs-nxhm-unipay-dmz-14 logs]#
[root@ecs-nxhm-unipay-dmz-14 logs]#
[root@ecs-nxhm-unipay-dmz-14 logs]# tail -f ./kubelet.ERROR
Log file created at: 2020/03/02 17:24:05
Running on machine: ecs-nxhm-unipay-dmz-14
Binary: Built with gc go1.7.6 for linux/amd64
Log line format: [IWEF]mmdd hh:mm:ss.uuuuuu threadid file:line] msg
E0302 17:24:05.813195   35352 kubelet.go:1165] Image garbage collection failed: unable to find data for container /
E0302 17:24:05.917829   35352 kubelet.go:1661] Failed to check if disk space is available for the runtime: failed to get fs info for "runtime": unable to find data for container /
E0302 17:24:05.917855   35352 kubelet.go:1669] Failed to check if disk space is available on the root partition: failed to get fs info for "root": unable to find data for container /
^C
```
