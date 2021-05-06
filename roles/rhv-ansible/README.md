Ansible Role: rhev-ansible
=================================

Description
-----------
This role is for vm creation on RHEV infrastructure.


Prerequisites
-------------
Before you run this role, make sure your workstation has the following package installed

**RHEL7**
```
python-ovirt-engine-sdk4-4.3.2-1.el7ev.x86_64.rpm
```

**RHEL8**
```
python3-ovirt-engine-sdk4-4.3.2-1.el8ev.x86_64.rpm
```


Parameters
----------

- **Variable:** `_rhv_vm`.\
      - Description: a dictionary of default values to combine with user dictionary\
      - Default value: all of the default vars\
      - **Note:** **don't change this var unnecessarily**

- **Variable:** `url`.\
      - Description: the rhv api url\
      - Example: https://rhvengine.example.com/ovirt-engine/api\
      - **Note:** **must be specified**

- **Variable:** `rhv_user`.\
      - Description: the rhv user you would like to connect to the API with\
      - Example: admin\
      - **Note** **must be specified**

- **Variable:** `rhv_password`.\
      - Description: the rhv user password\
      - Example: password\
      - **Note** **must be specified**

- **Variable:** `insecure`.\
      - Description: whether the machine you run the role from trusts the rhv certificate or not\
      - Example: true\
      - **Note** **must be specified**

- **Variable:** `template`.\
      - Description: the vm template\
      - Example: rhel76base\
      - **Note:** **must be specified**

- **Variable:** `memory`.\
      - Description: the vm ram in gigabytes\
      - Default value: 2GiB

- **Variable:** `cpu_cores`.\
      - Description: the amount of cpu cores the vm will have\
      - Default value: 1

- **Variable:** `cluster`.\
      - Description: the rhv cluster that the vm will belong to\
      - Default value: Default\
      - **Note:** **don't change this var unnecessarily**

- **Variable:** `type`.\
      - Description: the vm type (server or desktop)\
      - Default value: server\
      - **Note:** **don't change this var unnecessarily**

- **Variable:** `nics`.\
      - Description: a list of dictionaries that map nics and networks\
      - Default value: []\
      - Example:

```
   nics:
   - name: "nic1"
     network: "net-3"
     profile_name: "net-3"
   - name: "nic2"
     network: "net-4"
     profile_name: "net-4"
```

- **Variable:** `disks`.\
      - Description: a list of disk dictionaries\
      - Default value: []\
      - Example:

```
   disks:
   - disk_size: 25GiB
     name: disk1
     interface: "virtio_scsi"
   - disk_size: 100GiB
     name: disk2
     interface: "virtio_scsi"
```

- **Variable:** `cloud_init`.\
      - Description: a dictionary for cloud init configuration, can be used to configure a root password and one network card\
      - Default value: {}\
      - Example:

```
   cloud_init:
     user_name: "root"
     root_password: "1234"
```


- **Variable:** `cloud_init_nics`.\
      - Description: list of dictionary's for cloud init network configuration\
      - Default value: []\
      - **Note:** **the device names are logical so they should start with ethXX**\
      - Example:

```
   cloud_init_nics:
   - name: "eth0"
     boot_protocol: "static"
     ip: "0.0.1.126"
     netmask: "255.255.0.0"
     gateway: "0.0.255.254"
     on_boot: true
   - name: "eth1"
     boot_protocol: "static"
     ip: "0.0.1.125"
     netmask: "255.255.0.0"
     gateway: "0.0.255.254"
```

- **Variable:** `ansible_host`\
      - Description: the ip you want to be added to the inventory and to be associated with the hostname after the machine is created for further roles in an ansible workflow\
      - If this variable is not defined, then the host will not be added to the inventory, and you won't be able to access the vms in further roles\
      - Default value: no default\

- **Variable:** `ansible_group_list`\
      - Description: the inventory groups you want the host to be a part of\
      - by default the host is not added to any group\
      - Default value: no default\

To get the role into your /etc/ansible directory, run:

```
sudo ansible-galaxy install -r requirements.yml
```

requirements.yml example:

```
---
- name: rhev-ansible
  src: https://gitlab.com/michael.kot/rhev-ansible 
  scm: git
```


Example
-------
**example:**
Your playbook should just include a call to the role:
```
---

- hosts: localhost
  roles:
  - rhev-ansible
```

Create a directory called host_vars on the same relative path of your playbook and configure your vm parameters there:
```
---
rhv_vm:
  - name: "test01"
    template: "rhel76base"
    url: "https://rhvengine.example.com/ovirt-engine/api"
    rhv_user: "user"
    rhv_password: "password"
    insecure: "true"
    memory: "16GiB"
    cpu_cores: "8"
    nics:
    - name: "nic1"
      network: "ovirtmgmt"
      profile_name: "ovirtmgmt"
    - name: "nic2"
      network: "net-1"
      profile_name: "net-1"
    cloud_init:
      user_name: "root"
      root_password: "password" 
      host_name: "test01"
      nic_name: "eth0"
      nic_boot_protocol: "static"
      nic_ip_address: "10.10.99.137"
      nic_netmask: "255.255.0.0"
      nic_gateway: "10.10.255.254"
      nic_on_boot: true
    cloud_init_nics:
    - nic_name: "eth1"
      nic_boot_protocol: "static"
      nic_ip_address: "20.20.253.1"
      nic_netmask: "255.255.0.0"
      nic_on_boot: true
    disks:
    - name: "MyDisk-1"
      disk_size: "20GiB"
      interface: "virtio_scsi"
    - name: "MyDisk-2"
      disk_size: "15GiB"
      interface: "virtio_scsi"
    ansible_host: "0.0.99.137"
    ansible_group_list:
      - test_group1
      - test_group2

  - name: "test02"
    url: "https://rhvengine.example.com/ovirt-engine/api"
    rhv_user: "user"
    rhv_password: "password"
    insecure: "true"    
    template: "Blank"
    memory: "16GiB"
    cpu_cores: "8"
    state: "stopped"
    nics:
    - name: "nic1"
      network: "net-2"
      profile_name: "net-2"
    - name: "nic2"
      network: "net-1"
      profile_name: "net-1"

````
