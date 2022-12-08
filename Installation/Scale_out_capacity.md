# Scale out Storage capacity

## Create local volume
Using local storage operator to provision new local volume as SC "lso-sc":
```yaml
apiVersion: "local.storage.openshift.io/v1"
kind: "LocalVolume"
metadata:
  name: "odf-local-disks"
  namespace: "openshift-local-storage"
spec:
  managementState: Managed
  nodeSelector:
    nodeSelectorTerms:
    - matchExpressions:
        - key: cluster.ocs.openshift.io/openshift-odf
          operator: Exists
  tolerations:
    - key: node-role.kubernetes.io/worker
      effect: NoSchedule
  storageClassDevices:
    - storageClassName: "lso-sc"
      volumeMode: Block
      devicePaths:
        - /dev/nvme0n1
        - /dev/nvme1n1
        - /dev/nvme2n1
        - /dev/nvme3n1
        - /dev/nvme6n1
        - /dev/nvme7n1
        
 ```
Totally, 4x6=24 PVs are added to cluster.

## Add Capacity for StorageSystem on GUI
The 24 PVs will be divided into 8 storageDeviceSet (replia=3). It triggers the count is changed from '1' to '9' in StorageCluster.
```yaml
apiVersion: ocs.openshift.io/v1
kind: StorageCluster
metadata:
  annotations:
    uninstall.ocs.openshift.io/cleanup-policy: delete
    uninstall.ocs.openshift.io/mode: graceful
  resourceVersion: '124735411'
  name: ocs-storagecluster
......
spec:
  storageDeviceSets:
    - config: {}
      count: 9
      dataPVCTemplate:
        metadata: {}
        spec:
          accessModes:
            - ReadWriteOnce
          resources:
            requests:
              storage: '1'
          storageClassName: lso-sc
          volumeMode: Block
        status: {}
      name: ocs-deviceset-lso-sc
      placement: {}
      preparePlacement: {}
      replica: 3
      resources:
        limits:
          cpu: '2'
          memory: 5Gi
        requests:
          cpu: '1'
          memory: 5Gi
  encryption:
    kms: {}
  mirroring: {}
```

## Check PVCs are bound to PVs
```
$ oc get pvc
NAME                                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS                  AGE
db-noobaa-db-pg-0                    Bound     pvc-5e45f1fe-b860-4815-b857-06ecefcced3e   50Gi       RWO            ocs-storagecluster-ceph-rbd   33d
ocs-deviceset-lso-sc-0-data-0v9vwx   Bound     local-pv-110b30cc                          894Gi      RWO            lso-sc                        33d
ocs-deviceset-lso-sc-0-data-1gmd7f   Bound     local-pv-b5f8f6b5                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-0-data-2gcqwl   Bound     local-pv-d2240f2b                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-0-data-3rzr95   Bound     local-pv-c3621325                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-0-data-45grtk   Bound     local-pv-cbfe436d                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-0-data-5qxzpj   Bound     local-pv-b97a9d6d                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-0-data-6tf6d6   Bound     local-pv-a54a394d                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-0-data-7bljvg   Bound     local-pv-16b25669                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-0-data-87whrq   Bound     local-pv-622f30af                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-1-data-0zxr2k   Bound     local-pv-a1d72cb                           894Gi      RWO            lso-sc                        33d
ocs-deviceset-lso-sc-1-data-1x4q9f   Bound     local-pv-c40c4c67                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-1-data-2mk5px   Bound     local-pv-47f02a39                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-1-data-3x9qh5   Bound     local-pv-6d660bf                           2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-1-data-4zmmgw   Bound     local-pv-be5246cb                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-1-data-55r2vq   Bound     local-pv-39d51c45                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-1-data-6kfpkm   Bound     local-pv-22df5241                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-1-data-7nr5bb   Bound     local-pv-d924a9f8                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-1-data-8cwqsc   Bound     local-pv-6db8618b                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-2-data-0mb99j   Bound     local-pv-903de992                          894Gi      RWO            lso-sc                        33d
ocs-deviceset-lso-sc-2-data-1hfvb7   Bound     local-pv-9a98ddb6                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-2-data-2t2smh   Bound     local-pv-3f2a02c0                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-2-data-3tscrz   Bound     local-pv-b47e9721                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-2-data-4ldkbr   Bound     local-pv-7236d867                          2980Gi     RWO            lso-sc                        20h
ocs-deviceset-lso-sc-2-data-5zqr9n   Pending                                                                        lso-sc                        20h
ocs-deviceset-lso-sc-2-data-6g9m4l   Pending                                                                        lso-sc                        20h
ocs-deviceset-lso-sc-2-data-74nzwf   Pending                                                                        lso-sc                        20h
ocs-deviceset-lso-sc-2-data-8mmspn   Pending                                                                        lso-sc                        20h
```

There are 4 PVCs are not bound to PVs because of topologySpreadConstrains in pod. The node oc-odf-2 and oc-odf-3 are labeled with rack0 which unsatisfied topologySpreadConstraint.
```diff
$ oc get pod -o wide |grep rook-ceph-osd-prepare |grep Pending
rook-ceph-osd-prepare-f8bd79099445e744efb1b942a2007a67-r98h4      0/1     Pending     0             21h

$ oc get pod rook-ceph-osd-prepare-f8bd79099445e744efb1b942a2007a67-r98h4 -o yaml
......
  topologySpreadConstraints:
  - labelSelector:
      matchExpressions:
      - key: ceph.rook.io/pvc
        operator: Exists
    maxSkew: 1
    topologyKey: topology.rook.io/rack
    whenUnsatisfiable: DoNotSchedule
  - labelSelector:
      matchExpressions:
      - key: ceph.rook.io/pvc
        operator: Exists
    maxSkew: 1
    topologyKey: kubernetes.io/hostname
    whenUnsatisfiable: ScheduleAnyway

$ oc get node -L topology.rook.io/rack
NAME                            STATUS   ROLES           AGE   VERSION           RACK
oc-cntl-1.dm.fc38.fivegcn.com   Ready    master,worker   34d   v1.23.5+3afdacb   rack0
oc-cntl-2.dm.fc38.fivegcn.com   Ready    master,worker   34d   v1.23.5+3afdacb   rack1
oc-cntl-3.dm.fc38.fivegcn.com   Ready    master,worker   34d   v1.23.5+3afdacb   rack2
oc-odf-1.dm.fc38.fivegcn.com    Ready    worker          44h   v1.23.5+3afdacb   rack1
oc-odf-2.dm.fc38.fivegcn.com    Ready    worker          43h   v1.23.5+3afdacb   rack0
oc-odf-3.dm.fc38.fivegcn.com    Ready    worker          43h   v1.23.5+3afdacb   rack0
oc-odf-4.dm.fc38.fivegcn.com    Ready    worker          43h   v1.23.5+3afdacb   rack2

```
By re-label ndoe oc-odf-3 to rack3, all remained PVCs are bound to PVs.


## Check Ceph status
Before adding storage capacity:
```
$ oc rsh rook-ceph-operator-6985f85bcb-hh64c

sh-4.4$ export CEPH_ARGS='-c /var/lib/rook/openshift-storage/openshift-storage.config'
sh-4.4$ ceph df
--- RAW STORAGE ---
CLASS     SIZE    AVAIL     USED  RAW USED  %RAW USED
ssd    2.6 TiB  405 GiB  2.2 TiB   2.2 TiB      84.91
TOTAL  2.6 TiB  405 GiB  2.2 TiB   2.2 TiB      84.91
 
--- POOLS ---
POOL                                                   ID  PGS    STORED  OBJECTS     USED  %USED  MAX AVAIL
ocs-storagecluster-cephblockpool                        1  128   862 GiB  149.76k  1.7 TiB  99.89    949 MiB
device_health_metrics                                   2    1   2.2 MiB        3  6.7 MiB   0.35    632 MiB
ocs-storagecluster-cephobjectstore.rgw.control          3    8       0 B        8      0 B      0    949 MiB
ocs-storagecluster-cephobjectstore.rgw.meta             4    8   3.8 KiB       16  168 KiB      0    632 MiB
ocs-storagecluster-cephobjectstore.rgw.buckets.non-ec   5    8       0 B        0      0 B      0    632 MiB
ocs-storagecluster-cephobjectstore.rgw.log              6    8   1.1 MiB      340  4.8 MiB   0.25    695 MiB
ocs-storagecluster-cephobjectstore.rgw.buckets.index    7    8    29 MiB       22   87 MiB   4.36    632 MiB
.rgw.root                                               8    8   4.9 KiB       16  180 KiB      0    632 MiB
ocs-storagecluster-cephfilesystem-metadata              9   32  1000 KiB       22  3.0 MiB   0.16    632 MiB
ocs-storagecluster-cephobjectstore.rgw.buckets.data    10   32   183 GiB   87.45k  548 GiB  99.66    632 MiB
ocs-storagecluster-cephfilesystem-data0                11   32       0 B        0      0 B      0    632 MiB

sh-4.4$ ceph status
  cluster:
    id:     a3d9640b-6e5e-4ea3-9ca4-dcd5d320827b
    health: HEALTH_WARN
            3 backfillfull osd(s)
            1 OSDs or CRUSH {nodes, device-classes} have {NOUP,NODOWN,NOIN,NOOUT} flags set
            Low space hindering backfill (add storage if this doesn't resolve itself): 137 pgs backfill_toofull
            Degraded data redundancy: 149861/712902 objects degraded (21.021%), 137 pgs degraded, 137 pgs undersized
            68 pgs not deep-scrubbed in time
            8 pgs not scrubbed in time
            11 pool(s) backfillfull
 
  services:
    mon: 3 daemons, quorum a,b,c (age 18m)
    mgr: a(active, since 21m)
    mds: 1/1 daemons up, 1 hot standby
    osd: 3 osds: 3 up (since 19m), 3 in (since 4w); 137 remapped pgs
    rgw: 1 daemon active (1 hosts, 1 zones)
 
  data:
    volumes: 1/1 healthy
    pools:   11 pools, 273 pgs
    objects: 237.63k objects, 760 GiB
    usage:   2.2 TiB used, 404 GiB / 2.6 TiB avail
    pgs:     149861/712902 objects degraded (21.021%)
             137 active+undersized+degraded+remapped+backfill_toofull
             136 active+clean
 
  io:
    client:   4.0 MiB/s rd, 1.4 MiB/s wr, 65 op/s rd, 88 op/s wr
```

After adding storage capacity:
```
sh-4.4$ ceph df
--- RAW STORAGE ---
CLASS    SIZE   AVAIL     USED  RAW USED  %RAW USED
ssd    61 TiB  59 TiB  2.3 TiB   2.3 TiB       3.74
TOTAL  61 TiB  59 TiB  2.3 TiB   2.3 TiB       3.74
 
--- POOLS ---
POOL                                                   ID  PGS    STORED  OBJECTS     USED  %USED  MAX AVAIL
ocs-storagecluster-cephblockpool                        1  128   585 GiB  149.81k  1.7 TiB   3.44     16 TiB
device_health_metrics                                   2    1   2.2 MiB        3  6.7 MiB      0     16 TiB
ocs-storagecluster-cephobjectstore.rgw.control          3    8       0 B        8      0 B      0     16 TiB
ocs-storagecluster-cephobjectstore.rgw.meta             4    8   4.4 KiB       16  196 KiB      0     16 TiB
ocs-storagecluster-cephobjectstore.rgw.buckets.non-ec   5    8       0 B        0      0 B      0     16 TiB
ocs-storagecluster-cephobjectstore.rgw.log              6    8  1014 KiB      340  4.8 MiB      0     16 TiB
ocs-storagecluster-cephobjectstore.rgw.buckets.index    7    8    29 MiB       22   87 MiB      0     16 TiB
.rgw.root                                               8    8   6.3 KiB       16  216 KiB      0     16 TiB
ocs-storagecluster-cephfilesystem-metadata              9   32   1.1 MiB       22  3.3 MiB      0     16 TiB
ocs-storagecluster-cephobjectstore.rgw.buckets.data    10  128   185 GiB   87.45k  555 GiB   1.11     16 TiB
ocs-storagecluster-cephfilesystem-data0                11  128       0 B        0      0 B      0     16 TiB

sh-4.4$ ceph status
  cluster:
    id:     a3d9640b-6e5e-4ea3-9ca4-dcd5d320827b
    health: HEALTH_OK
 
  services:
    mon: 3 daemons, quorum a,b,c (age 5h)
    mgr: a(active, since 5h)
    mds: 1/1 daemons up, 1 hot standby
    osd: 23 osds: 23 up (since 54m), 23 in (since 54m)
    rgw: 1 daemon active (1 hosts, 1 zones)
 
  data:
    volumes: 1/1 healthy
    pools:   11 pools, 465 pgs
    objects: 237.66k objects, 760 GiB
    usage:   2.2 TiB used, 59 TiB / 61 TiB avail
    pgs:     465 active+clean
 
  io:
    client:   3.9 KiB/s rd, 164 MiB/s wr, 3 op/s rd, 6.47k op/s wr

```


Reference:
https://access.redhat.com/documentation/en-us/red_hat_openshift_data_foundation/4.10/html-single/scaling_storage/index#adding-capacity-with-3-OSDs-using-the-add-capacity-option_rhodf
