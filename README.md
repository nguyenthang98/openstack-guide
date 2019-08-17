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

(*Note: Keep the password table for your self and do not share it to other*)
##### Synchronize between services (NTP)
As recommended by OpenStack Installation Guilde, we're going to install Chrony, an implementation of Network Time Protocol (NTP).

 - On controller-vm:
		 - # `yum install chrony`
		 - edit the **/etc/chrony.conf** file:
		 `server 192.168.122.102 iburst`
		 (*Note: we are going to use management ip address of controller-vm to run chrony server*)
		 - to enable other nodes connect to chrony deamon add the following line to **chrony.conf** file:
		 `allow 192.168.122.0/24`
		 - restart the NTP service:
		# `service chronyd restart`
		or
		# `systemctl enable chronyd.service`
		# `systemctl start chronyd.service`
 - On compute-vm:
		-	install chrony same as controller node
		-	edit the **/etc/chrony.conf** file as follow:
		`server controller iburst`
		- restart the **chronyd** service
##### Add OpenStack package repositories to yum
Enable OpenStack extra repository for Stein release:
`yum install centos-release-openstack-stein`
`yum upgrade`
`yum install python-openstackclient`
`yum install openstack-selinux`
##### Install sql database for Controller node
- `yum install mariadb mariadb-server python2-PyMySQL`
- Create and edit **/etc/my.cnf.d/openstack.cnf**:
```
[mysqld]
bind-address = 192.168.122.102
default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
```
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
eyJoaXN0b3J5IjpbLTIwMTMyNzA1MTUsLTg5MDY5MjQwOSwxND
g4OTQxMDEsNTU2ODM1OTUyLC05NTAxODIwNjcsMjM4MDM3ODA4
LDY3NTc1NzUzOCw1MjkzMzg5MDMsLTI4Mjk3NzQ0MSwxNzU4OT
YxMzAsMjAyNjQ0Njg5MSw5NDAwMjA3MDQsLTMzMjQ1NTM2M119

-->