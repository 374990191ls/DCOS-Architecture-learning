# kubenetes daemonSet 不调度pod

> 问题issue：[DaemonSet didn't create pod after node have enough resource #67225](https://github.com/kubernetes/kubernetes/issues/67225)

由于k8s环境部署没有RBAC，且proxy是用daemonSet调度部署的，当新增主机时，新的node上不会跑proxy。

但是此现象也补全对，当前获得信息的主机上glance却是调度正常，也是daemonSet守护的。


**==部分情况下，k8s对于部分新增主机的部分的部分daemonSet调度不完整==**

具体现象
## node 状态
Ready

## ds值不匹配
```
[ecs_platform@ecs-xhm-test-10 kube]$ kubectl -n kube-system get ds -o wide
NAME         DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE       CONTAINER(S)   IMAGE(S)                        SELECTOR
kube-proxy   39        32        30        32           30          <none>          1h        kube-proxy     test.com:6999/kube-proxy:v1.6   app=kube-proxy
```
## ds 错误信息

一下为补全主机均为NotReady
```
Events:
  FirstSeen	LastSeen	Count	From			SubObjectPath	Type		Reason			Message
  ---------	--------	-----	----			-------------	--------	------			-------
  1h		14m		86	daemonset-controller			Normal		FailedPlacement		failed to place pod on "ecs-xhm-test-10.142.195.59": Node didn't have enough resource: pods, requested: 1, used: 0, capacity: 0
  13m		13m		1	daemon-set				Normal		SuccessfulCreate	Created pod: kube-proxy-q3409  # 部分自愈
  13m		12m		9	daemonset-controller			Normal		FailedPlacement		failed to place pod on "ecs-xhm-test-10.142.195.60": Node didn't have enough resource: pods, requested: 1, used: 0, capacity: 0
  12m		12m		1	daemon-set				Normal		SuccessfulCreate	Created pod: kube-proxy-j00t9   # 部分自愈
  1h		7m		110	daemonset-controller			Normal		FailedPlacement		failed to place pod on "ecs-xhm-k8s-10.20.42.30": Node didn't have enough resource: pods, requested: 1, used: 0, capacity: 0
  1h		7m		110	daemonset-controller			Normal		FailedPlacement		failed to place pod on "bj-upay-xhm-4f03-l3-m910-ingtw-01": Node didn't have enough resource: pods, requested: 1, used: 0, capacity: 0
  1h		7m		110	daemonset-controller			Normal		FailedPlacement		failed to place pod on "ecs-xhm-test-164": Node didn't have enough resource: pods, requested: 1, used: 0, capacity: 0
  1h		7m		110	daemonset-controller			Normal		FailedPlacement		failed to place pod on "bj-upay-xhm-4f03-l3-m910-ingtw-04": Node didn't have enough resource: pods, requested: 1, used: 0, capacity: 0
  1h		7m		110	daemonset-controller			Normal		FailedPlacement		failed to place pod on "bj-upay-xhm-4f03-l3-m910-ingtw-02": Node didn't have enough resource: pods, requested: 1, used: 0, capacity: 0
```



## Ready主机系统日志
```
Mar 23 18:24:42 ecs-xhm-test-10 systemd: Stopping Kubernetes Kubelet Server...
Mar 23 18:24:42 ecs-xhm-test-10 kubelet: 2020/03/23 18:24:42 transport: http2Server.HandleStreams failed to read frame: read unix /var/run/dockershim.sock->@: use of closed network connection
Mar 23 18:24:42 ecs-xhm-test-10 kubelet: 2020/03/23 18:24:42 transport: http2Client.notifyError got notified that the client transport was broken EOF.
Mar 23 18:24:42 ecs-xhm-test-10 systemd: kubelet.service: main process exited, code=exited, status=1/FAILURE
Mar 23 18:24:42 ecs-xhm-test-10 systemd: Unit kubelet.service entered failed state.
Mar 23 18:24:42 ecs-xhm-test-10 systemd: kubelet.service failed.
Mar 23 18:24:42 ecs-xhm-test-10 systemd: Started Kubernetes Kubelet Server.
Mar 23 18:24:42 ecs-xhm-test-10 systemd: Starting Kubernetes Kubelet Server...
Mar 23 18:24:45 ecs-xhm-test-10 kubelet: E0323 18:24:45.247095    5824 kubelet.go:1165] Image garbage collection failed: unable to find data for container /
Mar 23 18:24:45 ecs-xhm-test-10 kubelet: E0323 18:24:45.352448    5824 kubelet.go:1661] Failed to check if disk space is available for the runtime: failed to get fs info for "runtime": unable to find data for container /
Mar 23 18:24:45 ecs-xhm-test-10 kubelet: E0323 18:24:45.352481    5824 kubelet.go:1669] Failed to check if disk space is available on the root partition: failed to get fs info for "root": unable to find data for container /
Mar 23 18:24:45 ecs-xhm-test-10 kubelet: E0323 18:24:45.408213    5824 kubelet.go:1661] Failed to check if disk space is available for the runtime: failed to get fs info for "runtime": unable to find data for container /
Mar 23 18:24:45 ecs-xhm-test-10 kubelet: E0323 18:24:45.408247    5824 kubelet.go:1669] Failed to check if disk space is available on the root partition: failed to get fs info for "root": unable to find data for container /

Mar 23 18:26:26 ecs-xhm-test-10 kubelet: E0323 18:26:26.345452    5824 remote_runtime.go:203] StartContainer "8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03" from runtime service failed: rpc error: code = 2 desc = failed to create symbolic link "/var/log/pods/c1d59160-6cee-11ea-927f-848f6923087f/daemonset-glance_0.log" to the container log file "/app/docker/containers/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03-json.log" for container "8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03": symlink /app/docker/containers/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03-json.log /var/log/pods/c1d59160-6cee-11ea-927f-848f6923087f/daemonset-glance_0.log: file exists
Mar 23 18:26:26 ecs-xhm-test-10 kubelet: E0323 18:26:26.345545    5824 kuberuntime_manager.go:719] container start failed: rpc error: code = 2 desc = failed to create symbolic link "/var/log/pods/c1d59160-6cee-11ea-927f-848f6923087f/daemonset-glance_0.log" to the container log file "/app/docker/containers/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03-json.log" for container "8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03": symlink /app/docker/containers/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03-json.log /var/log/pods/c1d59160-6cee-11ea-927f-848f6923087f/daemonset-glance_0.log: file exists: Start Container Failed
Mar 23 18:26:26 ecs-xhm-test-10 kubelet: E0323 18:26:26.345720    5824 pod_workers.go:182] Error syncing pod c1d59160-6cee-11ea-927f-848f6923087f ("daemonset-glance-pg2lv_default(c1d59160-6cee-11ea-927f-848f6923087f)"), skipping: failed to "StartContainer" for "daemonset-glance" with rpc error: code = 2 desc = failed to create symbolic link "/var/log/pods/c1d59160-6cee-11ea-927f-848f6923087f/daemonset-glance_0.log" to the container log file "/app/docker/containers/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03-json.log" for container "8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03": symlink /app/docker/containers/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03/8553412bb2829871f78542797f27805ca0e46c429c798d5c7c7725fd8b594f03-json.log /var/log/pods/c1d59160-6cee-11ea-927f-848f6923087f/daemonset-glance_0.log: file exists: "Start Container Failed"
Mar 23 18:26:52 ecs-xhm-test-10 kubelet: E0323 18:26:52.029754    5824 fsHandler.go:121] failed to collect filesystem stats - rootDiskErr: du command failed on /app/docker/overlay/385e6175e219e259aac4123d5f700192d971dfdc24209b640d977a903a8b7889 with output stdout: , stderr: du: cannot access ‘/app/docker/overlay/385e6175e219e259aac4123d5f700192d971dfdc24209b640d977a903a8b7889’: No such file or directory
Mar 23 18:26:52 ecs-xhm-test-10 kubelet: - exit status 1, rootInodeErr: cmd [find /app/docker/overlay/385e6175e219e259aac4123d5f700192d971dfdc24209b640d977a903a8b7889 -xdev -printf .] failed. stderr: find: ‘/app/docker/overlay/385e6175e219e259aac4123d5f700192d971dfdc24209b640d977a903a8b7889’: No such file or directory
Mar 23 18:26:52 ecs-xhm-test-10 kubelet: ; err: exit status 1, extraDiskErr: du command failed on /app/docker/containers/691b1e7b858b7b29a13618935bf1561ba0fc00d2120bbb336b28111b91570070 with output stdout: , stderr: du: cannot access ‘/app/docker/containers/691b1e7b858b7b29a13618935bf1561ba0fc00d2120bbb336b28111b91570070’: No such file or directory
Mar 23 18:26:52 ecs-xhm-test-10 kubelet: - exit status 1

Mar 23 18:28:45 ecs-xhm-test-10 kubelet: E0323 18:28:45.268984    5824 docker_sandbox.go:205] Failed to stop sandbox "e3330309292845261020ac3e25d24947736e6a57fcd0c947007ce425f4bd3a6e": Error response from daemon: {"message":"No such container: e3330309292845261020ac3e25d24947736e6a57fcd0c947007ce425f4bd3a6e"}
Mar 23 18:28:45 ecs-xhm-test-10 dockerd: time="2020-03-23T18:28:45.269608740+08:00" level=error msg="Handler for DELETE /v1.24/containers/e3330309292845261020ac3e25d24947736e6a57fcd0c947007ce425f4bd3a6e returned error: No such container: e3330309292845261020ac3e25d24947736e6a57fcd0c947007ce425f4bd3a6e"
Mar 23 18:28:51 ecs-xhm-test-10 kubelet: E0323 18:28:51.587189    5824 fsHandler.go:121] failed to collect filesystem stats - rootDiskErr: du command failed on /app/docker/overlay/75a5a685a6220ac25de3cb556887c40fb7ed7cb374587bf9a8c1db8bc14c20bd with output stdout: , stderr: du: cannot access ‘/app/docker/overlay/75a5a685a6220ac25de3cb556887c40fb7ed7cb374587bf9a8c1db8bc14c20bd’: No such file or directory
Mar 23 18:28:51 ecs-xhm-test-10 kubelet: - exit status 1, rootInodeErr: cmd [find /app/docker/overlay/75a5a685a6220ac25de3cb556887c40fb7ed7cb374587bf9a8c1db8bc14c20bd -xdev -printf .] failed. stderr: find: ‘/app/docker/overlay/75a5a685a6220ac25de3cb556887c40fb7ed7cb374587bf9a8c1db8bc14c20bd’: No such file or directory
Mar 23 18:28:51 ecs-xhm-test-10 kubelet: ; err: exit status 1, extraDiskErr: du command failed on /app/docker/containers/e3330309292845261020ac3e25d24947736e6a57fcd0c947007ce425f4bd3a6e with output stdout: , stderr: du: cannot access ‘/app/docker/containers/e3330309292845261020ac3e25d24947736e6a57fcd0c947007ce425f4bd3a6e’: No such file or directory
Mar 23 18:28:51 ecs-xhm-test-10 kubelet: - exit status 1
```


==Failed to check if disk space is available for the runtime==

- - -


## 解决办法*【未验证准确】

1. 处理主机知道Ready
2. 尝试手动创建proxy
```
# 测试/11及老版本
$ docker run -dt --network=host --privileged test.com:6999/kube-proxy:v1.6 \
  kube-proxy --master=http://kubernetes.vos.com:8080 \
  --v=0 --log-dir=/var/log \
  --iptables-sync-period=5s \
  --masquerade-all=true

# 生产高版本 TODO镜像版本不一样
$ docker run -dt --network=host --privileged test.com:6999/kube-proxy:v1.6 \
  kube-proxy --master=http://kubernetes.vos.com:8080 \
  --v=0 --log-dir=/var/log \
  --masquerade-all=true \
  --proxy-mode=ipvs \
  --ipvs-min-sync-period=5s \
  --ipvs-sync-period=5s \
  --ipvs-scheduler=rr \
  --iptables-sync-period=10s
```
3. 等待系统自愈
