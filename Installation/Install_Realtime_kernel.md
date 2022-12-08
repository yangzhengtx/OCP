### Install Realtime Kernel manually

```diff
$ oc debug node/oc-wrkr-6
Starting pod/oc-wrkr-6-debug ...
To use host binaries, run `chroot /host`
Pod IP: 10.38.200.110
If you don't see a command prompt, try pressing enter.
 
sh-4.4# chroot /host
 
sh-4.4# rpm-ostree status
State: idle
Deployments:
* pivot://quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:f69b86f903f69f8d2473da3ce28237074ea250b9d474eb33649a3baaebd96d32
              CustomOrigin: Managed by machine-config-operator
                   Version: 410.84.202206010432-0 (2022-06-01T04:35:53Z)

  pivot://quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:f69b86f903f69f8d2473da3ce28237074ea250b9d474eb33649a3baaebd96d32
              CustomOrigin: Managed by machine-config-operator
                   Version: 410.84.202206010432-0 (2022-06-01T04:35:53Z)
sh-4.4#  mkdir -p /run/mco-machine-os-content/os-content/
 
sh-4.4# oc image extract --path /:/run/mco-machine-os-content/os-content --registry-config /var/lib/kubelet/config.json  quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:f69b86f903f69f8d2473da3ce2237074ea250b9d474eb33649a3baaebd96d32
 
sh-4.4# cd /run/mco-machine-os-content/os-content/extensions/
 
sh-4.4# ls
extensions.json					       kernel-rt-core-4.18.0-305.49.1.rt7.121.el8_4.x86_64.rpm		 qemu-kiwi-5.2.0-16.module+el8.4.0+14193+48a3f5f5.15.x86_64.rpm
ipxe-roms-qemu-20181214-8.git133f4c47.el8.noarch.rpm   kernel-rt-devel-4.18.0-305.49.1.rt7.121.el8_4.x86_64.rpm		 qemu-kvm-common-5.2.0-16.module+el8.4.0+14193+48a3f5f5.15.x86_64.rpm
kata-containers-2.3.0-3.el8.x86_64.rpm		       kernel-rt-kvm-4.18.0-305.49.1.rt7.121.el8_4.x86_64.rpm		 repodata
kernel-4.18.0-305.49.1.el8_4.x86_64.rpm		       kernel-rt-modules-4.18.0-305.49.1.rt7.121.el8_4.x86_64.rpm	 seabios-bin-1.14.0-1.module+el8.4.0+8855+a9e237a9.noarch.rpm
kernel-core-4.18.0-305.49.1.el8_4.x86_64.rpm	       kernel-rt-modules-extra-4.18.0-305.49.1.rt7.121.el8_4.x86_64.rpm  seavgabios-bin-1.14.0-1.module+el8.4.0+8855+a9e237a9.noarch.rpm
kernel-devel-4.18.0-305.49.1.el8_4.x86_64.rpm	       libpmem-1.6.1-1.el8.x86_64.rpm					 sgabios-bin-0.20170427git-3.module+el8.4.0+8855+a9e237a9.noarch.rpm
kernel-headers-4.18.0-305.49.1.el8_4.x86_64.rpm        libqb-1.0.3-12.el8.x86_64.rpm					 usbguard-1.0.0-2.el8.x86_64.rpm
kernel-modules-4.18.0-305.49.1.el8_4.x86_64.rpm        pixman-0.38.4-1.el8.x86_64.rpm					 usbguard-selinux-1.0.0-2.el8.noarch.rpm
kernel-modules-extra-4.18.0-305.49.1.el8_4.x86_64.rpm  protobuf-3.5.0-13.el8.x86_64.rpm

sh-4.4# ls *rt*
kernel-rt-core-4.18.0-305.49.1.rt7.121.el8_4.x86_64.rpm   kernel-rt-kvm-4.18.0-305.49.1.rt7.121.el8_4.x86_64.rpm      kernel-rt-modules-extra-4.18.0-305.49.1.rt7.121.el8_4.x86_64.rpm
kernel-rt-devel-4.18.0-305.49.1.rt7.121.el8_4.x86_64.rpm  kernel-rt-modules-4.18.0-305.49.1.rt7.121.el8_4.x86_64.rpm

sh-4.4# cat << EOF > /etc/yum.repos.d/coreos-extensions.repo
> [coreos-extensions]
> enabled=1
> metadata_expire=1m
> baseurl=/run/mco-machine-os-content/os-content/extensions/
> gpgcheck=0
> skip_if_unavailable=False
> EOF
 
sh-4.4# rpm-ostree override remove kernel{,-core,-modules,-modules-extra} --install kernel-rt-core --install kernel-rt-modules --install kernel-rt-modules-extra --install kernel-rt-kvm
Checking out tree 415b4fb... done
Enabled rpm-md repositories: coreos-extensions
rpm-md repo 'coreos-extensions' (cached); generated: 2022-06-01T05:10:52Z
Importing rpm-md... done
Resolving dependencies... done
Importing packages... done
Applying 4 overrides and 4 overlays
Processing packages... done
Running pre scripts... done
Running post scripts... done
Running posttrans scripts... done
Writing rpmdb... done
Generating initramfs... done
Writing OSTree commit... done
Staging deployment... done
Removed:
  kernel-4.18.0-305.49.1.el8_4.x86_64
  kernel-core-4.18.0-305.49.1.el8_4.x86_64
  kernel-modules-4.18.0-305.49.1.el8_4.x86_64
  kernel-modules-extra-4.18.0-305.49.1.el8_4.x86_64
Added:
  kernel-rt-core-4.18.0-305.49.1.rt7.121.el8_4.x86_64
  kernel-rt-kvm-4.18.0-305.49.1.rt7.121.el8_4.x86_64
  kernel-rt-modules-4.18.0-305.49.1.rt7.121.el8_4.x86_64
  kernel-rt-modules-extra-4.18.0-305.49.1.rt7.121.el8_4.x86_64
Run "systemctl reboot" to start a reboot
sh-4.4# systemctl reboot
sh-4.4# exit
sh-4.4# 
Removing debug pod ...
```

After rebooting.
```
[core@oc-wrkr-6 ~]$ uname -a
Linux oc-wrkr-6 4.18.0-305.49.1.rt7.121.el8_4.x86_64 #1 SMP PREEMPT_RT Wed May 11 16:23:29 EDT 2022 x86_64 x86_64 x86_64 GNU/Linux
 
[core@oc-wrkr-6 ~]$ rpm-ostree status
State: idle
Deployments:
● pivot://quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:f69b86f903f69f8d2473da3ce28237074ea250b9d474eb33649a3baaebd96d32
              CustomOrigin: Managed by machine-config-operator
                   Version: 410.84.202206010432-0 (2022-06-01T04:35:53Z)
       RemovedBasePackages: kernel-core kernel-modules kernel kernel-modules-extra 4.18.0-305.49.1.el8_4
           LayeredPackages: kernel-rt-core kernel-rt-kvm kernel-rt-modules kernel-rt-modules-extra

  pivot://quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:f69b86f903f69f8d2473da3ce28237074ea250b9d474eb33649a3baaebd96d32
              CustomOrigin: Managed by machine-config-operator
                   Version: 410.84.202206010432-0 (2022-06-01T04:35:53Z)

```



Reference:
https://access.redhat.com/solutions/6927171
