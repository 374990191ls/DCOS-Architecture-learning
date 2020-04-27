# kube-proxy API样例


## 查询代理模式

实践
```
[root@host ~]# curl -l 'http://0.0.0.0:10249/proxyMode'
ipvs
```

可作为健康检查api使用
> 参考`test/e2e/framework/util.go`
> ```
> curl -q -s --connect-timeout 1 http://localhost:10249/proxyMode
> ```



## 性能报告

```bash
[root@host ~]# curl -l 'http://0.0.0.0:10249/metrics'
# HELP apiserver_audit_event_total Counter of audit events generated and sent to the audit backend.
# TYPE apiserver_audit_event_total counter
apiserver_audit_event_total 0
# HELP apiserver_audit_requests_rejected_total Counter of apiserver requests rejected due to an error in audit logging backend.
# TYPE apiserver_audit_requests_rejected_total counter
apiserver_audit_requests_rejected_total 0
# HELP go_gc_duration_seconds A summary of the GC invocation durations.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 0.000111987
go_gc_duration_seconds{quantile="0.25"} 0.000124511
go_gc_duration_seconds{quantile="0.5"} 0.000129654
go_gc_duration_seconds{quantile="0.75"} 0.000136605
go_gc_duration_seconds{quantile="1"} 0.000182847
go_gc_duration_seconds_sum 4228.285987448
go_gc_duration_seconds_count 3.1492061e+07
# HELP go_goroutines Number of goroutines that currently exist.
# TYPE go_goroutines gauge
go_goroutines 42
# HELP go_memstats_alloc_bytes Number of bytes allocated and still in use.
# TYPE go_memstats_alloc_bytes gauge
go_memstats_alloc_bytes 1.2173072e+07
# HELP go_memstats_alloc_bytes_total Total number of bytes allocated, even if freed.
# TYPE go_memstats_alloc_bytes_total counter
go_memstats_alloc_bytes_total 2.4300546246232e+14
# HELP go_memstats_buck_hash_sys_bytes Number of bytes used by the profiling bucket hash table.
# TYPE go_memstats_buck_hash_sys_bytes gauge
go_memstats_buck_hash_sys_bytes 2.956912e+06
# HELP go_memstats_frees_total Total number of frees.
# TYPE go_memstats_frees_total counter
go_memstats_frees_total 1.55689300698e+12
# HELP go_memstats_gc_cpu_fraction The fraction of this program's available CPU time used by the GC since the program started.
# TYPE go_memstats_gc_cpu_fraction gauge
go_memstats_gc_cpu_fraction 0.0010984568591234742
# HELP go_memstats_gc_sys_bytes Number of bytes used for garbage collection system metadata.
# TYPE go_memstats_gc_sys_bytes gauge
go_memstats_gc_sys_bytes 2.486272e+06
# HELP go_memstats_heap_alloc_bytes Number of heap bytes allocated and still in use.
# TYPE go_memstats_heap_alloc_bytes gauge
go_memstats_heap_alloc_bytes 1.2173072e+07
# HELP go_memstats_heap_idle_bytes Number of heap bytes waiting to be used.
# TYPE go_memstats_heap_idle_bytes gauge
go_memstats_heap_idle_bytes 4.4097536e+07
# HELP go_memstats_heap_inuse_bytes Number of heap bytes that are in use.
# TYPE go_memstats_heap_inuse_bytes gauge
go_memstats_heap_inuse_bytes 1.642496e+07
# HELP go_memstats_heap_objects Number of allocated objects.
# TYPE go_memstats_heap_objects gauge
go_memstats_heap_objects 111379
# HELP go_memstats_heap_released_bytes Number of heap bytes released to OS.
# TYPE go_memstats_heap_released_bytes gauge
go_memstats_heap_released_bytes 0
# HELP go_memstats_heap_sys_bytes Number of heap bytes obtained from system.
# TYPE go_memstats_heap_sys_bytes gauge
go_memstats_heap_sys_bytes 6.0522496e+07
# HELP go_memstats_last_gc_time_seconds Number of seconds since 1970 of last garbage collection.
# TYPE go_memstats_last_gc_time_seconds gauge
go_memstats_last_gc_time_seconds 1.584612901582386e+09
# HELP go_memstats_lookups_total Total number of pointer lookups.
# TYPE go_memstats_lookups_total counter
go_memstats_lookups_total 0
# HELP go_memstats_mallocs_total Total number of mallocs.
# TYPE go_memstats_mallocs_total counter
go_memstats_mallocs_total 1.556893118359e+12
# HELP go_memstats_mcache_inuse_bytes Number of bytes in use by mcache structures.
# TYPE go_memstats_mcache_inuse_bytes gauge
go_memstats_mcache_inuse_bytes 69120
# HELP go_memstats_mcache_sys_bytes Number of bytes used for mcache structures obtained from system.
# TYPE go_memstats_mcache_sys_bytes gauge
go_memstats_mcache_sys_bytes 81920
# HELP go_memstats_mspan_inuse_bytes Number of bytes in use by mspan structures.
# TYPE go_memstats_mspan_inuse_bytes gauge
go_memstats_mspan_inuse_bytes 309472
# HELP go_memstats_mspan_sys_bytes Number of bytes used for mspan structures obtained from system.
# TYPE go_memstats_mspan_sys_bytes gauge
go_memstats_mspan_sys_bytes 475136
# HELP go_memstats_next_gc_bytes Number of heap bytes when next garbage collection will take place.
# TYPE go_memstats_next_gc_bytes gauge
go_memstats_next_gc_bytes 1.750968e+07
# HELP go_memstats_other_sys_bytes Number of bytes used for other system allocations.
# TYPE go_memstats_other_sys_bytes gauge
go_memstats_other_sys_bytes 4.745864e+06
# HELP go_memstats_stack_inuse_bytes Number of bytes in use by the stack allocator.
# TYPE go_memstats_stack_inuse_bytes gauge
go_memstats_stack_inuse_bytes 6.586368e+06
# HELP go_memstats_stack_sys_bytes Number of bytes obtained from system for stack allocator.
# TYPE go_memstats_stack_sys_bytes gauge
go_memstats_stack_sys_bytes 6.586368e+06
# HELP go_memstats_sys_bytes Number of bytes obtained from system.
# TYPE go_memstats_sys_bytes gauge
go_memstats_sys_bytes 7.7854968e+07
# HELP go_threads Number of OS threads created
# TYPE go_threads gauge
go_threads 84
# HELP http_request_duration_microseconds The HTTP request latencies in microseconds.
# TYPE http_request_duration_microseconds summary
http_request_duration_microseconds{handler="prometheus",quantile="0.5"} NaN
http_request_duration_microseconds{handler="prometheus",quantile="0.9"} NaN
http_request_duration_microseconds{handler="prometheus",quantile="0.99"} NaN
http_request_duration_microseconds_sum{handler="prometheus"} 0
http_request_duration_microseconds_count{handler="prometheus"} 0
# HELP http_request_size_bytes The HTTP request sizes in bytes.
# TYPE http_request_size_bytes summary
http_request_size_bytes{handler="prometheus",quantile="0.5"} NaN
http_request_size_bytes{handler="prometheus",quantile="0.9"} NaN
http_request_size_bytes{handler="prometheus",quantile="0.99"} NaN
http_request_size_bytes_sum{handler="prometheus"} 0
http_request_size_bytes_count{handler="prometheus"} 0
# HELP http_response_size_bytes The HTTP response sizes in bytes.
# TYPE http_response_size_bytes summary
http_response_size_bytes{handler="prometheus",quantile="0.5"} NaN
http_response_size_bytes{handler="prometheus",quantile="0.9"} NaN
http_response_size_bytes{handler="prometheus",quantile="0.99"} NaN
http_response_size_bytes_sum{handler="prometheus"} 0
http_response_size_bytes_count{handler="prometheus"} 0
# HELP kubeproxy_sync_proxy_rules_latency_microseconds SyncProxyRules latency
# TYPE kubeproxy_sync_proxy_rules_latency_microseconds histogram
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="1000"} 1
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="2000"} 1
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="4000"} 1
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="8000"} 1
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="16000"} 1
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="32000"} 1
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="64000"} 1
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="128000"} 1
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="256000"} 324952
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="512000"} 1.779169e+06
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="1.024e+06"} 1.779216e+06
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="2.048e+06"} 1.779221e+06
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="4.096e+06"} 1.779223e+06
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="8.192e+06"} 1.779223e+06
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="1.6384e+07"} 1.779223e+06
kubeproxy_sync_proxy_rules_latency_microseconds_bucket{le="+Inf"} 1.779223e+06
kubeproxy_sync_proxy_rules_latency_microseconds_sum 4.79881454531e+11
kubeproxy_sync_proxy_rules_latency_microseconds_count 1.779223e+06
# HELP kubernetes_build_info A metric with a constant '1' value labeled by major, minor, git version, git commit, git tree state, build date, Go version, and compiler from which Kubernetes was built, and platform on which it is running.
# TYPE kubernetes_build_info gauge
kubernetes_build_info{buildDate="2019-02-28T13:35:32Z",compiler="gc",gitCommit="c27b913fddd1a6c480c229191a087698aa92f0b1",gitTreeState="clean",gitVersion="v1.13.4",goVersion="go1.11.5",major="1",minor="13",platform="linux/amd64"} 1
# HELP process_cpu_seconds_total Total user and system CPU time spent in seconds.
# TYPE process_cpu_seconds_total counter
process_cpu_seconds_total 818711.6
# HELP process_max_fds Maximum number of open file descriptors.
# TYPE process_max_fds gauge
process_max_fds 1024
# HELP process_open_fds Number of open file descriptors.
# TYPE process_open_fds gauge
process_open_fds 184
# HELP process_resident_memory_bytes Resident memory size in bytes.
# TYPE process_resident_memory_bytes gauge
process_resident_memory_bytes 5.8916864e+07
# HELP process_start_time_seconds Start time of the process since unix epoch in seconds.
# TYPE process_start_time_seconds gauge
process_start_time_seconds 1.57526993754e+09
# HELP process_virtual_memory_bytes Virtual memory size in bytes.
# TYPE process_virtual_memory_bytes gauge
process_virtual_memory_bytes 1.4655488e+08
# HELP rest_client_request_latency_seconds Request latency in seconds. Broken down by verb and URL.
# TYPE rest_client_request_latency_seconds histogram
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="0.001"} 0
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="0.002"} 0
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="0.004"} 0
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="0.008"} 12
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="0.016"} 12
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="0.032"} 14
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="0.064"} 16
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="0.128"} 16
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="0.256"} 16
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="0.512"} 16
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET",le="+Inf"} 666
rest_client_request_latency_seconds_sum{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET"} 6290.576363214994
rest_client_request_latency_seconds_count{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="GET"} 666
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="0.001"} 0
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="0.002"} 0
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="0.004"} 0
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="0.008"} 0
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="0.016"} 0
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="0.032"} 0
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="0.064"} 0
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="0.128"} 1
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="0.256"} 1
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="0.512"} 1
rest_client_request_latency_seconds_bucket{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST",le="+Inf"} 1
rest_client_request_latency_seconds_sum{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST"} 0.096650585
rest_client_request_latency_seconds_count{url="http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D",verb="POST"} 1
# HELP rest_client_requests_total Number of HTTP requests, partitioned by status code, method, and host.
# TYPE rest_client_requests_total counter
rest_client_requests_total{code="200",host="kubernetes-cluster1.vos.com:8080",method="GET"} 41510
rest_client_requests_total{code="201",host="kubernetes-cluster1.vos.com:8080",method="POST"} 1
rest_client_requests_total{code="<error>",host="kubernetes-cluster1.vos.com:8080",method="GET"} 663
```
> 注：`http://kubernetes-cluster1.vos.com:8080/%7Bprefix%7D`解码为`http://kubernetes-cluster1.vos.com:8080/{prefix}`
