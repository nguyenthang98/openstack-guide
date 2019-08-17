# OpenStack comprehensive tutorial
## Abstract
This document is based on official OpenStack installation and deployment guide.
## Install OpenStack
### System information
OS: CentOS 7
RAM: 12GiB
CPUS: 8 core
DISK: 16GiB
### Architecture
We will install minimal deployment for Stein release of OpenStack with these services:
 - Identity service (Keystone)
 - Image service (Glance)
 - Placement service (Placement)
 - Compute service (Nova)
 - Networking service (Neutron)

We also install front-end dashboard(Horizon) to launch new instance easier

We will install OpenStack with following architecture

### Prerequisite
 - The host machine already installed libvirt and bridge-utils
 - Basic understanding of virtual machine management with libvirt
 - Understanding linux bridge and how to use it
 - Feel comfortable when using linux commands ;-)
### Conventions
 

### Setting up environment
#### Install dependencies
- install libvirt and qemu with yum: `yum install libvirt qemu-kvm`
- get CentOS cloud image at: [CentOS 7](http://cloud.centos.org/centos/7/images/)
#### Create controller machine
(create controller machine script)
#### Setting up persistent network and ssh authentication
(networking script)
#### Security
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
#### Synchronize between services (NTP)
As recommended by OpenStack Installation Guilde, we're going to install Chrony, an implementation of Network Time Protocol (NTP).

 - On controller-vm:
		 - # `yum install chrony`
		 - edit the **/etc/chrony.conf** file:
		 `server 192.168.122.102 iburst`
		 (*Note: we are going to use management ip address of controller-vm to run chrony server*)
		 - to enable other nodes connect to chrony deamon add the following line to **chrony.conf** file:
		`allow 192.168.122.0/24`
		 - restart the NTP service:
		`service chronyd restart`
		or
	```bash
	systemctl enable chronyd.service
	systemctl start chronyd.service
	```
 - On compute-vm:
		-	install chrony same as controller node
		-	edit the **/etc/chrony.conf** file as follow:
		`server controller iburst`
		- restart the **chronyd** service
#### Add OpenStack package repositories to yum
Enable OpenStack extra repository for Stein release:
```bash
yum install centos-release-openstack-stein
yum upgrade
yum install python-openstackclient
yum install openstack-selinux
```
#### Install sql database for Controller node
- `yum install mariadb mariadb-server python2-PyMySQL`
- Create and edit **/etc/my.cnf.d/openstack.cnf**:
	```ini
	[mysqld]
	bind-address = 192.168.122.102
	default-storage-engine = innodb
	innodb_file_per_table = on
	max_connections = 4096
	collation-server = utf8_general_ci
	character-set-server = utf8
	```
- Enable mariadb on startup and start db
	```bash
	systemctl enable mariadb
	systemctl start mariadb
	```
- After mysql database running, use `mysql_secure_installation` to secure your database.
#### Install Message-queue
```bash
yum install rabbitmq-server
systemctl enable rabbitmq-server.service
systemctl start rabbitmq-server.service
```
Add openstack user
`rabbitmqctl add_user openstack RABBIT_PASS` (*Note: replace **RABBIT_PASS** with the suitable password on the password table you created before*)
`rabbitmqctl set_permissions openstack ".*" ".*" ".*"`
#### Install Memcached
`yum install memcached python-memcached`
Edit the **/etc/sysconfig/memcached** file:
`OPTIONS="-l 127.0.0.1,::1,controller"` (*Note: change the existing line `OPTIONS="-l 127.0.0.1,::1"`*)
Enable memcached on startup and start it:
```bash
systemctl enable memcached.service
systemctl start memcached.service
```
#### Install Ectd (a distributed reliable key-value store)
`yum install etcd`
Edit the **/etc/etcd/etcd.conf** file and set the `ETCD_INITIAL_CLUSTER`, `ETCD_INITIAL_ADVERTISE_PEER_URLS`, `ETCD_ADVERTISE_CLIENT_URLS`, `ETCD_LISTEN_CLIENT_URLS` to the management IP address of the controller node.
```ini
#[Member]
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="http://192.168.122.102:2380"
ETCD_LISTEN_CLIENT_URLS="http://192.168.122.102:2379"
ETCD_NAME="controller"
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://192.168.122.102:2380"
ETCD_ADVERTISE_CLIENT_URLS="http://192.168.122.102:2379"
ETCD_INITIAL_CLUSTER="controller=http://192.168.122.102:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster-01"
ETCD_INITIAL_CLUSTER_STATE="new"
```
restart service
```bash
systemctl enable etcd
systemctl start etcd
```
### Install OpenStack services - Stein Release
#### Install Identity Service (Keystone)
##### Prerequisites

Create keystone database:
 ```bash
 $ mysql -u root -p
MariaDB [(none)]> CREATE DATABASE keystone;
MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
IDENTIFIED BY 'KEYSTONE_DBPASS';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
IDENTIFIED BY 'KEYSTONE_DBPASS';
 ```
 (*Note: replace KEYSTONE_DBPASS with suitable password in the password table you created before*)
##### Install and configure components
- Install packages:
	`yum install openstack-keystone httpd mod_wsgi`
- Edit the **/etc/keystone/keystone.conf** file:
	- in **[database]** section:
	```ini
	[database]
	connection=mysql+pymysql://keystone:KEYSTONE_DBPASS@controller/keystone
	```
	replace **KEYSTONE_DBPASS** by the password you created before
	- in **[token]** section:
	```ini
	[token]
	provider=fernet
	```
- Populate the Identity service database:
```bash
su -s /bin/sh -c "keystone-manage db_sync" keystone
```
- Initialize Fernet key repositories:
```bash
keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
keystone-manage credential_setup --keystone-user keystone --keystone-group keystone
```
- Bootstrap the Identity service:
```bash
keystone-manage bootstrap --bootstrap-password ADMIN_PASS \
  --bootstrap-admin-url http://controller:5000/v3/ \
  --bootstrap-internal-url http://controller:5000/v3/ \
  --bootstrap-public-url http://controller:5000/v3/ \
  --bootstrap-region-id RegionOne
```
(*Note: replace ADMIN_PASS with password you created before*)
##### Configure the Apache HTTP server
- edit the **etc/httpd/conf/httpd.conf** file:
	```ini
	ServerName controller
	```
- create a link to the **/usr/share/keystone/wsgi-keystone.conf** file:
	```bash
	ln -s /usr/share/keystone/wsgi-keystone.conf /etc/httpd/conf.d/
	```
##### Finalize the Installation
```bash
systemctl enable httpd.service
systemctl start httpd.service
```
Configure adminitrative account
```bash
export OS_USERNAME=admin
export OS_PASSWORD=ADMIN_PASS
export OS_PROJECT_NAME=admin
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_DOMAIN_NAME=Default
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3
```
##### Create a domain, projects, users, and roles
```bash

# create default domain
openstack domain create --description "An Example Domain" example

# create project service
openstack project create --domain default \
  --description "Service Project" service

# create unprivileged project named myproject for regular tasks
openstack project create --domain default \
  --description "Demo Project" myproject
  
# create myuser user and enter password for this user
openstack user create --domain default \
  --password-prompt myuser
  
# create new role named myrole
openstack role create myrole

# Add the `myrole` role to the `myproject` project and `myuser`user
openstack role add --project myproject --user myuser myrole
```
##### Verify operations
- Unset temporary some environment variables
```bash
unset OS_AUTH_URL OS_PASSWORD
```
- As the **`admin`** user, request an authentication token:
```bash
openstack --os-auth-url http://controller:5000/v3 \
>   --os-project-domain-name Default --os-user-domain-name Default \
>   --os-project-name admin --os-username admin token issue
Password: 
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field      | Value                                                                                                                                                                                   |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| expires    | 2019-08-17T15:10:23+0000                                                                                                                                                                |
| id         | gAAAAABdWArPhNfbwEDyQ1U0VPvPEVaibyE4rF6a7f0sJ-cLezCyOSrFJCRjasIFo59nTD6SUIc_Q0OGPOESql8mfMFW-3DZuRGbo5t6pOBjESR_3A4Jvvny4qXSXMShoMwCLSjGPWSfslEJCbD-6mWsPoEeOFPKOiTDE4MWzqHDFbmv8qKnjyw |
| project_id | 5dc95d1c667e44f299c1ac890c3ec989                                                                                                                                                        |
| user_id    | bb1d9312525b4b79837b91c26fc3803e                                                                                                                                                        |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```
- As the **`myuser`** user created in the previous section, request an authentication token
```bash
 openstack --os-auth-url http://controller:5000/v3 \
>   --os-project-domain-name Default --os-user-domain-name Default \
>   --os-project-name myproject --os-username myuser token issue
Password: 
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field      | Value                                                                                                                                                                                   |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| expires    | 2019-08-17T15:11:56+0000                                                                                                                                                                |
| id         | gAAAAABdWAss7-ReISh56nERdKSYw7783062_yURjlzT-WOVMgAP-g0GlY4NdOu0MumxplL2daWGj_d1ruFYcEiWogAbgjk3YCfvjqhDVvs56at_kaZGNH1y_6g4vI_iPxN5ZkRr78Lyk1b2MNJyC2vracQoh4m-2xs0ps85OOPs48-rYutPHY4 |
| project_id | 49dccc8266e44c5b9a8583e5eab97577                                                                                                                                                        |
| user_id    | 05394a5accc94362a519881d0f80cff4                                                                                                                                                        |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```
##### Create client environment script
- create **`admin-rc`** file:
```bash
export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=ADMIN_PASS
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
```
(*Note: replace ADMIN_PASS by suitable password*)
- create **`demo-rc`** file:
```bash
export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=myproject
export OS_USERNAME=myuser
export OS_PASSWORD=MYUSER_PASS
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
```
(*Note: replace MYUSER_PASS with suitable password*)

To use resource file: `. <filename>`

#### Install Image Service (Glance)

##### Create glance database
```bash
$ mysql -u root -p
MariaDB [(none)]> CREATE DATABASE glance;
MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' \
 IDENTIFIED BY 'GLANCE_DBPASS';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' \
 IDENTIFIED BY 'GLANCE_DBPASS';
```


#### Install Placement Service (Placement)
(Installation script)
#### Install Compute Service (Nova)
(Nova Installation script)
#### Install Networking Service (Neutron)
(Neutron installation script)

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzNjA4Njk3ODEsLTEwODM3MjQ1MDcsLT
EwMDk4ODA3MTQsMjc4NDU2MTQxLC05ODQ0MjA0NDksLTEwNjIw
MzA4NTMsNDU3NDYwODc1LC04OTA2OTI0MDksMTQ4ODk0MTAxLD
U1NjgzNTk1MiwtOTUwMTgyMDY3LDIzODAzNzgwOCw2NzU3NTc1
MzgsNTI5MzM4OTAzLC0yODI5Nzc0NDEsMTc1ODk2MTMwLDIwMj
Y0NDY4OTEsOTQwMDIwNzA0LC0zMzI0NTUzNjNdfQ==
-->