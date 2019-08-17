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
##### Security
You can chose any password you want. But i recommend you generate them using a tool such as [pwgen](https://sourceforge.net/projects/pwgen/)  or running the following command `openssl rand -hex 10` (*Note: Replace 10 by password length which comfort to you*);
|Variable|Description|
|--|--|
|`ADMIN_PASS` | Password of user  `admin` |
`CINDER_DBPASS` | Database password for the Block Storage service |`CINDER_PASS`|Password of Block Storage service user  `cinder`|
|`DASH_DBPASS`|Database password for the Dashboard|
|`DEMO_PASS`|Password of user  `demo`|
|`GLANCE_DBPASS`|Database password for Image service|
|`GLANCE_PASS`|Password of Image service user  `glance`|
|`KEYSTONE_DBPASS`|Database password of Identity service|
|`METADATA_SECRET`|Secret for the metadata proxy|
|`NEUTRON_DBPASS`|Database password for the Networking service |
|`NEUTRON_PASS`|Password of Networking service user  `neutron`|
|`NOVA_DBPASS`|Database password for Compute service|
|`NOVA_PASS`|Password of Compute service user  `nova`|
|`PLACEMENT_PASS`|Password of the Placement service user  `placement`|
|`RABBIT_PASS`|Password of RabbitMQ user  `openstack`|

(*Note: Keep the password gth*)

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
eyJoaXN0b3J5IjpbLTE3OTk3MzIxNzUsMjM4MDM3ODA4LDY3NT
c1NzUzOCw1MjkzMzg5MDMsLTI4Mjk3NzQ0MSwxNzU4OTYxMzAs
MjAyNjQ0Njg5MSw5NDAwMjA3MDQsLTMzMjQ1NTM2M119
-->