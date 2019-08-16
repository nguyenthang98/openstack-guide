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
#### Conventions
 

#### Setting up environment
##### Install dependencies
- install libvirt and qemu with yum: `yum install libvirt qemu-kvm`
- get CentOS cloud image at: [CentOS 7](http://cloud.centos.org/centos/7/images/)
##### Create controller machine
(create controller machine script)
##### Setting up persistent network and ssh authentication
(networking script)
#### Install OpenStack - Stein Release
##### Install Identity Service (Keystone)
(Keystone installation script)
##### Install Image Service (Glance)
(Installation script)
##### Install Placement Service (Placement)
(Installation script)
##### Install Compute Service (Nova)
(Nova Installation script)
##### Install Networking Service (Neutron)
(Neutron installation script)

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkwNzgzNDQ3NSwyMzgwMzc4MDgsNjc1Nz
U3NTM4LDUyOTMzODkwMywtMjgyOTc3NDQxLDE3NTg5NjEzMCwy
MDI2NDQ2ODkxLDk0MDAyMDcwNCwtMzMyNDU1MzYzXX0=
-->