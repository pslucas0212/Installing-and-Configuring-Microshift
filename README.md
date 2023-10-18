# Installing and Configuring Microshift

## Setup
For this lab I created a Red Hat Enterprise Linux 9.2 VM with 2 vCPUs, 8 Gib of RAM, 40GB drive and a second 20GB drive.  

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
Update system to latest code  
```
$ sudo dnf update -y  && sudo dnf upgrade -y
```
## Note: ## Seperate disk for PVC is a work in progres and can be skipped - Skip to  Installing MicroShift from and RPM Package section

Check the drives available to our VM
```
$ sudo fdisk -l
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: Virtual disk    
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sda: 60 GiB, 64424509440 bytes, 125829120 sectors
Disk model: Virtual disk    
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: CAC3561B-534A-4B69-99E3-82B271DB61C1

Device       Start       End   Sectors  Size Type
/dev/sda1     2048   1230847   1228800  600M EFI System
/dev/sda2  1230848   3327999   2097152    1G Linux filesystem
/dev/sda3  3328000 125827071 122499072 58.4G Linux LVM
...
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
  Physical volume "/dev/sdb" successfully created.
$ sudo vgcreate vmdisk /dev/sdb
  Volume group "vmdisk" successfully created
```

## Installing MicroShift from and RPM Package

```
$ sudo dnf -y install microshift openshift-clients
```

Copy pull-secret to the /etc/crio folder on our RHEL VM and set permissions


