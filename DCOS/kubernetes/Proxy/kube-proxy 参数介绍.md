# kube-proxy 参数介绍

> ref: https://www.colabug.com/592888.html
- - -

kube-proxy 的功能相对简单一些，也比较独立，需要的配置并不是很多，比较常用的启动参数包括：

|参数|含义|默认值|
|----|----|----|
|–alsologtostderr			|打印日志到标准输出		|false|
|–bind-address				|HTTP 监听地址	|0.0.0.0|
|–cleanup-iptables			|如果设置为 true，会清理 proxy 设置的 iptables 选项并退出	|false|
|–healthz-bind-address		|健康检查 HTTP API 监听端口	|127.0.0.1|
|–healthz-port				|健康检查端口	|10249|
|–iptables-masquerade-bit   |使用 iptables 进行 SNAT 的掩码长度	|14|
|–iptables-sync-period      |iptables 更新频率	|30s|
|–kubeconfig				|kubeconfig 配置文件地址| |
|–log-dir	                |日志文件目录/路径|	
|–masquerade-all			|如果使用 iptables 模式，对所有流量进行 SNAT 处理	|false|
|–master					|kubernetes master API Server 地址	||
|–proxy-mode				|代理模式， userspace 或者 iptables ， 目前默认是 iptables ，如果系统或者 iptables 版本不够新，会 fallback 到 userspace 模式	|iptables|
|–proxy-port-range			|代理使用的端口范围， 格式为 beginPort-endPort ，如果没有指定，会随机选择	|0-0|
|–udp-timeout				|UDP 空连接 timeout 时间，只对 userspace 模式有用	|250ms|
|–v							|日志级别	|0|
kube-proxy 					|的工作模式可以通过 --proxy-mode 进行配置，可以选择 userspace 或者 iptables 。||
