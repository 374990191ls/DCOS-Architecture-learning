
# privileged使用时报错

---
> ref: https://www.iteye.com/blog/crabdave-2367356
---

Kubernetes 解决spec.template.spec.containers[0].securityContext.privileged: Forbidden: disallowed by policy问题


kube-apiserver和kubelet的启动脚本中添加·--allow_privileged=true·，如果不添加的话，下面在部署calico的时候

 
```
vi /etc/sysconfig/kube-apiserver

KUBE_APISERVER_OPTS='--allow_privileged=true'



systemctl daemon-reload
systemctl restart kube-apiserver
systemctl status -l kube-apiserver
curl -L http://10.99.0.10:8080/healthz
```
 

节点机器上
```
vi /etc/sysconfig/kubelet
KUBELET_OPTS='--allow_privileged=true'


systemctl daemon-reload
systemctl restart kubelet
systemctl status -l kubelet1
```
