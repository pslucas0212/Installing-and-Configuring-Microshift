# Installing and Configuring Microshift

### Setup
For this lab I created a Red Hat Enterprise Linux 9.2 VM with 2 vCPUs, 4 Gib of RAM, 100GB drive and a second 20GB drive.  

Register they system.
```
$ sudo subscription-manager register --org=xxxxxxxxx --activationkey=your-activation-key
```

Enable additional repos
```
$ sudo subscription-manager repos --enable rhel-9-for-x86_64-supplementary-rpms
$ sudo subscription-manager repos --enable=rhocp-4.13-for-rhel-9-x86_64-rpms
$ sudo subscription-manager repos --enable=fast-datapath-for-rhel-9-x86_64-rpms
```

Check the repos enabled
```
$ sudo subscription-manager repos --list-enabled
+----------------------------------------------------------+
    Available Repositories in /etc/yum.repos.d/redhat.repo
+----------------------------------------------------------+
Repo ID:   fast-datapath-for-rhel-9-x86_64-rpms
Repo Name: Fast Datapath for RHEL 9 x86_64 (RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/layered/rhel9/x86_64/fast-datapath/os
Enabled:   1

Repo ID:   rhel-9-for-x86_64-supplementary-rpms
Repo Name: Red Hat Enterprise Linux 9 for x86_64 - Supplementary (RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/rhel9/$releasever/x86_64/supplementary/os
Enabled:   1

Repo ID:   rhel-9-for-x86_64-baseos-rpms
Repo Name: Red Hat Enterprise Linux 9 for x86_64 - BaseOS (RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/rhel9/$releasever/x86_64/baseos/os
Enabled:   1

Repo ID:   rhel-9-for-x86_64-appstream-rpms
Repo Name: Red Hat Enterprise Linux 9 for x86_64 - AppStream (RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/rhel9/$releasever/x86_64/appstream/os
Enabled:   1

Repo ID:   rhocp-4.13-for-rhel-9-x86_64-rpms
Repo Name: Red Hat OpenShift Container Platform 4.13 for RHEL 9 x86_64 (RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/layered/rhel9/x86_64/rhocp/4.13/os
Enabled:   1
```

Check the drives available to our VM
```
$ sudo fdisk -l
Disk /dev/sda: 100 GiB, 107374182400 bytes, 209715200 sectors
Disk model: Virtual disk    
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 037CB9A2-4FB7-428C-AFBD-4F0BE6CFB8DF

Device       Start       End   Sectors  Size Type
/dev/sda1     2048   1230847   1228800  600M EFI System
/dev/sda2  1230848   3327999   2097152    1G Linux filesystem
/dev/sda3  3328000 209713151 206385152 98.4G Linux LVM


Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: Virtual disk    
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: AC52DABE-3CE6-4D40-86A2-405037ABBDF8
```

We will set up this disk: Disk /dev/sdb: 20 GiB as the LVM storage for MicroShift.  Let's install required packages for LVM:
```
$ sudo dnf -y install lvm2
Updating Subscription Management repositories.
...
Complete!

```


Create a physical volume and volume group named **vmdisk** on the extr drive (**/dev/sdb** in our example).  
```
$ sudo pvcreate /dev/sdb
```
