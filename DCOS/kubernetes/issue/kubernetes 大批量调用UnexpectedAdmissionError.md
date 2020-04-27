# kubernetes 大批量调用UnexpectedAdmissionError

> ref: https://stackoverflow.com/questions/58455833/kubernetes-unexpectedadmissionerror-after-rollout
- - -

```
backend-54769cbb4-xkwf2              0/1     UnexpectedAdmissionError   0          4h6m
backend-54769cbb4-xlpgf              0/1     UnexpectedAdmissionError   0   
```


相关events:


```
Warning UnexpectedAdmissionError: Update plugin resources failed due to failed to write checkpoint file "kubelet_internal_checkpoint": write /var/lib/kubelet/device-plugins/.319590684: no space left on device, which is unexpected.
```
- - -
相关pod的json信息
<details>

```
{
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
        "creationTimestamp": "2020-03-26T15:05:36Z",
        "generateName": "mall-1.x-sa-iface-",
        "labels": {
            "name": "mall-1.x-sa-iface",
            "vos.app.cfg.app_type": "RESIDENT",
            "vos.app.cfg.health": "1",
            "vos.app.cfg.version": "2020-03-26T22_19_44.324038Z"
        },
        "name": "mall-1.x-sa-iface-zgrmh",
        "namespace": "mall-1",
        "ownerReferences": [
            {
                "apiVersion": "v1",
                "blockOwnerDeletion": true,
                "controller": true,
                "kind": "ReplicationController",
                "name": "mall-1.x-sa-iface",
                "uid": "e10926aa-6f6c-11ea-b2e9-30e1716f9694"
            }
        ],
        "resourceVersion": "2087144807",
        "selfLink": "/api/v1/namespaces/mall-1/pods/mall-1.x-sa-iface-zgrmh",
        "uid": "3f4e901b-6f73-11ea-86f3-30e1716f860c"
    },
    "spec": {
        "containers": [
            {
                "env": [
                    {
                        "name": "AS_PARTITION_ID",
                        "value": "mall-1"
                    },
                    {
                        "name": "AS_APP_ID",
                        "value": "/x-sa-iface"
                    },
                    {
                        "name": "AS_TASK_ID",
                        "valueFrom": {
                            "fieldRef": {
                                "apiVersion": "v1",
                                "fieldPath": "metadata.name"
                            }
                        }
                    },
                    {
                        "name": "AS_NODE_NAME",
                        "valueFrom": {
                            "fieldRef": {
                                "apiVersion": "v1",
                                "fieldPath": "spec.nodeName"
                            }
                        }
                    },
                    {
                        "name": "AS_MAX_TASK_RELAUNCH",
                        "value": "1"
                    },
                    {
                        "name": "AS_POD_CURR_RESTART_COUNT",
                        "value": "0"
                    },
                    {
                        "name": "VOS_APP_VERSION",
                        "value": "2020-03-26T22:19:44.324038Z"
                    },
                    {
                        "name": "AS_URL",
                        "value": "as.vos.com:5001"
                    }
                ],
                "image": "hub.ecs.com:6999/x-sa-iface:20200326205752",
                "imagePullPolicy": "IfNotPresent",
                "livenessProbe": {
                    "failureThreshold": 5,
                    "httpGet": {
                        "path": "/health/check",
                        "port": 22778,
                        "scheme": "HTTP"
                    },
                    "initialDelaySeconds": 15,
                    "periodSeconds": 60,
                    "successThreshold": 1,
                    "timeoutSeconds": 20
                },
                "name": "mall-1-x-sa-iface",
                "readinessProbe": {
                    "failureThreshold": 3,
                    "httpGet": {
                        "path": "/health/check",
                        "port": 22778,
                        "scheme": "HTTP"
                    },
                    "initialDelaySeconds": 15,
                    "periodSeconds": 60,
                    "successThreshold": 1,
                    "timeoutSeconds": 20
                },
                "resources": {},
                "terminationMessagePath": "/dev/termination-log",
                "terminationMessagePolicy": "File",
                "volumeMounts": [
                    {
                        "mountPath": "/root/pinpoint-agent-1.8.2",
                        "name": "volume-0"
                    },
                    {
                        "mountPath": "/app/logs",
                        "name": "volume-1"
                    }
                ]
            }
        ],
        "dnsPolicy": "ClusterFirst",
        "enableServiceLinks": true,
        "imagePullSecrets": [
            {
                "name": "ccc"
            }
        ],
        "nodeName": "ecs-nxhm-mall-k8s-125-177",
        "nodeSelector": {
            "domain": "mall-cluster-1"
        },
        "restartPolicy": "Always",
        "schedulerName": "default-scheduler",
        "securityContext": {},
        "terminationGracePeriodSeconds": 0,
        "volumes": [
            {
                "hostPath": {
                    "path": "/app/pinpoint-agent-1.8.2",
                    "type": ""
                },
                "name": "volume-0"
            },
            {
                "hostPath": {
                    "path": "/app/logs",
                    "type": ""
                },
                "name": "volume-1"
            }
        ]
    },
    "status": {
        "message": "Pod Update plugin resources failed due to failed to write checkpoint file \"kubelet_internal_checkpoint\": write /var/lib/kubelet/device-plugins/.873211270: no space left on device, which is unexpected.",
        "phase": "Failed",
        "reason": "UnexpectedAdmissionError",
        "startTime": "2020-03-26T15:05:36Z"
    }
}
```
</details>

- - -

pod描述信息
<details>

```
[interface@ecs-nxhm-interface-docker-57 ~]$ kubectl -n mall-1 describe  po mall-1.x-sa-iface-zgrmh
Name:           mall-1.x-sa-iface-zgrmh
Namespace:      mall-1
Node:           ecs-nxhm-mall-k8s-125-177/
Start Time:     Thu, 26 Mar 2020 23:05:36 +0800
Labels:         name=mall-1.x-sa-iface
                vos.app.cfg.app_type=RESIDENT
                vos.app.cfg.health=1
                vos.app.cfg.version=2020-03-26T22_19_44.324038Z
Annotations:    <none>
Status:         Failed
Reason:         UnexpectedAdmissionError
Message:        Pod Update plugin resources failed due to failed to write checkpoint file "kubelet_internal_checkpoint": write /var/lib/kubelet/device-plugins/.873211270: no space left on device, which is unexpected.
IP:
Controlled By:  ReplicationController/mall-1.x-sa-iface
Containers:
  mall-1-x-sa-iface:
    Image:      hub.ecs.com:6999/x-sa-iface:20200326205752
    Port:       <none>
    Host Port:  <none>
    Liveness:   http-get http://:22778/health/check delay=15s timeout=20s period=60s #success=1 #failure=5
    Readiness:  http-get http://:22778/health/check delay=15s timeout=20s period=60s #success=1 #failure=3
    Environment:
      AS_PARTITION_ID:            mall-1
      AS_APP_ID:                  /x-sa-iface
      AS_TASK_ID:                 mall-1.x-sa-iface-zgrmh (v1:metadata.name)
      AS_NODE_NAME:                (v1:spec.nodeName)
      AS_MAX_TASK_RELAUNCH:       1
      AS_POD_CURR_RESTART_COUNT:  0
      VOS_APP_VERSION:            2020-03-26T22:19:44.324038Z
      AS_URL:                     as.vos.com:5001
    Mounts:
      /app/logs from volume-1 (rw)
      /root/pinpoint-agent-1.8.2 from volume-0 (rw)
Volumes:
  volume-0:
    Type:          HostPath (bare host directory volume)
    Path:          /app/pinpoint-agent-1.8.2
    HostPathType:
  volume-1:
    Type:          HostPath (bare host directory volume)
    Path:          /app/logs
    HostPathType:
QoS Class:         BestEffort
Node-Selectors:    domain=mall-cluster-1
Tolerations:       <none>
Events:            <none>
```
</details>


- - -
## 解决方法
综上所述，可知是root-dir所在磁盘爆了，
1. 释放/var/lib/kubernetes所在盘空间
2. 定制kubelet的root-dir参数到大磁盘路径，重启kubelet

### 相关清理命令
```
[root@host ~]$ kubectl -n mall-1 get po -o wide |grep UnexpectedAdmissionError | wc -l
1419
[root@host ~]$ kubectl -n mall-1 delete po `kubectl -n mall-1 get po -o wide |grep UnexpectedAdmissionError | awk '{print $1}'`
...

# 或者
kubectl get po -o wide --all-namespaces |grep UnexpectedAdmissionError|awk -vOFS=, -vn=$i '{print "kubectl -n "$1" delete po "$2}'|sh
```
- - -
