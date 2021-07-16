# Inventory

This playbook expects three host groups to be defined, each host group must have at least 3 nodes. The inventory, as shown below, assumes the user you are using to `ssh` into the nodes is able to get elevated rights, and uses `sudo` for that. That behavior can be changed using `ansible_*` variables (see Ansible documentation). 

> Note: Theoretically, the playbook should have no problem with any number above 3 hosts per group, however, it has not been tested with larger host groups.

Please use the following structure for your inventory. You can use hostnames or IP addresses:

```
[all:vars]
ansible_user=<replace with the user to use for ssh access>
ansible_connection=ssh
ansible_become=true
ansible_become_user=root
ansible_become_method=sudo

[all:children]
master
worker
loadbalancer

[master:vars]
role=master

[worker:vars]
role=worker

[loadbalancer:vars]
role=loadbalancer

[master]
1.1.1.1
2.2.2.2
3.3.3.3

[worker]
4.4.4.4
5.5.5.5
6.6.6.6

[loadbalancer]
7.7.7.7
```