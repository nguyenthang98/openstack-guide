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
 - Understanding linux bridge and
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTM4MzE5NjM3LDY3NTc1NzUzOCw1MjkzMz
g5MDMsLTI4Mjk3NzQ0MSwxNzU4OTYxMzAsMjAyNjQ0Njg5MSw5
NDAwMjA3MDQsLTMzMjQ1NTM2M119
-->