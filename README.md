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

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1MjA3OTQ4MDQsNjc1NzU3NTM4LDUyOT
MzODkwMywtMjgyOTc3NDQxLDE3NTg5NjEzMCwyMDI2NDQ2ODkx
LDk0MDAyMDcwNCwtMzMyNDU1MzYzXX0=
-->