# OpenStack comprehensive tutorial
## Abstract
This document is based on official OpenStack installation and deployment guide.
## Install OpenStack
#### System information
OS: CentOS 7
RAM: 12GiB
CPUS: 8 core
DISK: 16GiB
#### Architecture
We will install minimal deployment for Stein release of OpenStack with these services:
 - Identity service (Keystone)
 - Image service (Glance)
 - Placement service (Placement)
 - Compute service (Nova)
 - Networking service (Neutron)

We also install front-end dashboard(Horizon) to launch new instance easier

We will install OpenStack with following architecture

#### Prerequisite
 - The host machine already installed libvirt and bridge-utils
 - Basic understanding of virtual machine management with libvirt
 - Understanding linux bridge and how to use it
 - Feel comfortable when using linux commands ;-)

#### Setting up environment
##### Install dependencies
- install libvirt and qemu with yum: `yum install libvirt qemu-kvm`
- get CentOS cloud image at: [CentOS 7](http://cloud.centos.org/centos/7/images/)
##### Create controller machine
- creat
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTM5NjkyMjAxNiw2NzU3NTc1MzgsNTI5Mz
M4OTAzLC0yODI5Nzc0NDEsMTc1ODk2MTMwLDIwMjY0NDY4OTEs
OTQwMDIwNzA0LC0zMzI0NTUzNjNdfQ==
-->