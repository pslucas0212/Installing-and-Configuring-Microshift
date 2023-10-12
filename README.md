# Installing and Configuring Microshift

### Setup
For this lab I created a Red Hat Enterprise Linux 9.2 VM with 2 vCPUs, 4 Gib of RAM, 100GB drive and a second 20GB drive.  

Register they system.
```
$ sudo subscription-manager register --org=xxxxxxxxx --activationkey=your-activation-key
```

Enable additional repos
```
$ sudo subscription-manager repos -enable rhel-9-for-x86_64-supplementary-rpms rhocp-4.13-for-rhel-9-x86_64-rpmsfast-datapath-for-rhel-9-x86_64-rpms
```
