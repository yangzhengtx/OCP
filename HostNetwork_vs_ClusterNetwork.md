## Host Network
If a pod runs in the host network of the node where the pod is deployed, the pod can use the network namespace and network resources of the node. In this case, the pod can access loopback devices, listen to addresses, and monitor the traffic of other pods on the node.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  hostNetwork: true
  containers:
  - name: nginx
    image: nginx
 
$ oc get pod -o wide
multus-gdwrc                          1/1     Running   0          31d   10.38.200.103   oc-cntl-2   <none>           <none>
multus-gh9w2                          1/1     Running   31         31d   10.38.200.107   oc-wrkr-3   <none>           <none>
multus-l7zdh                          1/1     Running   20         31d   10.38.200.109   oc-wrkr-5   <none>           <none>
multus-l9rjl                          1/1     Running   0          31d   10.38.200.104   oc-cntl-3   <none>           <none>
multus-z4fbx                          1/1     Running   25         31d   10.38.200.106   oc-wrkr-2   <none>           <none>

10.38.200.103 is the node IP address.

$ oc exec -it multus-gdwrc -- bash
# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens10f0: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 9000 qdisc mq master bond0 state UP group default qlen 1000
    link/ether 42:ec:21:18:94:60 brd ff:ff:ff:ff:ff:ff permaddr 88:e9:a4:4c:03:f0
3: ens10f1: <NO-CARRIER,BROADCAST,MULTICAST,SLAVE,UP> mtu 9000 qdisc mq master bond0 state DOWN group default qlen 1000
    link/ether 42:ec:21:18:94:60 brd ff:ff:ff:ff:ff:ff permaddr 88:e9:a4:4c:03:f1
4: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 9000 qdisc noqueue master ovs-system state UP group default qlen 1000
    link/ether 42:ec:21:18:94:60 brd ff:ff:ff:ff:ff:ff
5: ovs-system: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 0e:6f:5e:e5:4e:35 brd ff:ff:ff:ff:ff:ff
6: br-ex: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9000 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether 88:e9:a4:4c:03:f0 brd ff:ff:ff:ff:ff:ff
    inet 10.38.200.103/22 brd 10.38.203.255 scope global noprefixroute br-ex
       valid_lft forever preferred_lft forever
......

The network namespace on node is accessed by this pod.
 ```
 
 
 
 
## Cluster Network

```
$ oc get pod -o wide
network-metrics-daemon-4lpm8          2/2     Running   0          31d   10.140.0.6      oc-cntl-2   <none>           <none>
network-metrics-daemon-8q7lb          2/2     Running   53         31d   10.142.2.4      oc-wrkr-3   <none>           <none>
network-metrics-daemon-cvhwr          2/2     Running   47         31d   10.143.2.5      oc-wrkr-2   <none>           <none>
network-metrics-daemon-gnzhj          2/2     Running   0          31d   10.141.2.3      oc-cntl-3   <none>           <none>
network-metrics-daemon-pzxs2          2/2     Running   42         31d   10.140.2.4      oc-wrkr-7   <none>           <none>
network-metrics-daemon-q4z46          2/2     Running   30         31d   10.142.0.4      oc-wrkr-6   <none>           <none>
network-metrics-daemon-qs7k8          2/2     Running   2          31d   10.141.0.5      oc-cntl-1   <none>           <none>
network-metrics-daemon-rpn58          2/2     Running   35         31d   10.143.0.4      oc-wrkr-5   <none>           <none>

10.143.0.4 is the IP address of cluster network.

$ oc exec -it network-metrics-daemon-4lpm8 -- bash
Defaulted container "network-metrics-daemon" out of: network-metrics-daemon, kube-rbac-proxy
bash-4.4$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
3: eth0@if45: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 8900 qdisc noqueue state UP group default 
    link/ether 0a:58:0a:8c:00:06 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.140.0.6/23 brd 10.140.1.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::858:aff:fe8c:6/64 scope link 
       valid_lft forever preferred_lft forever

The individual cluster network namespace is assigned for this pod.
```
