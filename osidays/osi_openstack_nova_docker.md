## Docker as hypervisor driver for Openstack Nova Compute
Neependra Khare, Red Hat
<br/>

---

## What is Docker ?
It is an open platform for developers and sysadmins to build, ship, and run distributed applications.
-

## Traditional
![alt text](https://raw.githubusercontent.com/nkhare/presetations/master/osidays/images/traditional.png "Traditional IT")
-
## Virt and IaaS
![alt text](https://raw.githubusercontent.com/nkhare/presetations/master/osidays/images/virt-and-iaas.png "Virtualization and IaaS")
-
## Containerization
![alt text](https://raw.githubusercontent.com/nkhare/presetations/master/osidays/images/bg-containerized.png "Containerization")

---
## Docker Support in OpenStack - Nova
![alt text](https://wiki.openstack.org/w/images/6/6c/Docker-under-the-hood.png "Nova and Docker")

---
## Docker Support in OpenStack - Nova

+ Docker driver (Since Havana)
+ Driver is not in main source code tree
	- Faster Dev Cycle, Would be merged in Future
+ Implements OpenStack API

---
## Setup with DevStack on Fedora 20

+ Install Docker
```$ yum install docker-io
```
+ Clone nova-docker and devstack
```
$ git clone https://git.openstack.org/stackforge/nova-docker /opt/stack/nova-docker
$ git clone https://git.openstack.org/openstack-dev/devstack /opt/stack/devstack
```
+ Note : only needed until we can make use of configure_nova_hypervisor_rootwrap
```
git clone https://git.openstack.org/openstack/nova /opt/stack/nova
```
+ Prepare the devstack for installation
```
$ cd /opt/stack/nova-docker
$ ./contrib/devstack/prepare_devstack.sh
```
+ Start the Devstack setup
```
./stack.sh
```

---
## Setup with DevStack on Fedora 20
prepare_devstack.sh makes following changes :-
in localrc file, appends following :-
```
export VIRT_DRIVER=docker
export DEFAULT_IMAGE_NAME=cirros
export NON_STANDARD_REQS=1
export IMAGE_URLS=" "

```

---
## Setup with DevStack on Fedora 20

prepare_devstack.sh makes following changes 

+ in nova.conf file, changes the compute driver
```[DEFAULT]
compute_driver = novadocker.virt.docker.DockerDriver
```

+ Adds /etc/nova/rootwrap.d/docker.filters file with following content
```[Filters]
# nova/virt/docker/driver.py: 'ln', '-sf', '/var/run/netns/.*'
ln: CommandFilter, /bin/ln, root
```

+  in glance.conf, adds docker as container/image format
```[DEFAULT]
container_formats = ami,ari,aki,bare,ovf,docker
```

---

## Import a docker image to glance

```
$ source openrc
$ export OS_USERNAME=admin
$ docker pull fedora
$ sudo docker save fedora | glance image-create --is-public=True \
 --container-format=docker --disk-format=raw --name fedora
$ glance image-list
```
---
## Docker plugin for heat (Openstack Orchestration)
![alt text](http://blog.docker.com/wp-content/uploads/2014/03/heat-nova.png "Docker plugin for heat")

+ Deploy and manage containers
+ Native Docker API

---
## References
+ https://wiki.openstack.org/wiki/Docker
+ http://www.slideshare.net/ThanassisParathyras/docker-in-openstack
+ 
