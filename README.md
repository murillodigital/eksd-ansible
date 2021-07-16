# Minimal and Opinionated EKS-D Ansible Installer

This repository contains playbooks to deploy a minimalistic and lightly opinionated Amazon EKS-D Kubernetes Distribution. Kubernetes is installed in a Highly Available configuration using `kubeadm` with at least three control plane nodes, but defaults to automatically generated TLS certificates for control plane authentication as well as self-signed certificates for API.

Also consider that, although the Kubernetes installation is highly available, the NGINX deployment that directs traffic to the Kubernetes API is **not** highly available and, because the open source version of NGINX is used, no active health checks are in place. These caveats will be addressed in upcoming iterations.

There are certain _opinions_ defined in the playbooks:

- `nginx` is used for load balancing, and a single `nginx` node has been used by default.
- `docker` is used for container runtime - this will soon be replaced for `containerd`
- [`weave-net`](https://www.weave.works/oss/net/) is used for networking plugin, and currently receives a hardcoded value for `pod` CIDR.

> Note: The hardcoded `pod` CIDR range may collide with existing routes in your system depending on your specific network configuration. If you run into this issue, edit the hardcoded value in the applicable playbook

## Compatible Linux Distributions

You will find two versions of the playbooks available, one for `Ubuntu`, tested on version `18.04` and another for `CentOS` tested on version `7`. The playbooks may result in unexpected behavior if tested on other versions or distributions.

## How to use this playbook

### 1. Clone the repo, create a `virtualenv` and install `ansible`

```shell
git clone https://github.com/murillodigital/eksd-ansible.git
python3 -m venv ./eksd-ansible
cd eksd-ansible
. bin/activate
pip install -r requirements.txt
```

> Note: If you have `ansible` globally installed you may not have to do this. Commands will vary if you are using `python2`

### 2. Create an Inventory

You will need at least seven hosts, running either `CentOS 7` or `Ubuntu 18.04` (see note on [compatibility](#compatible-linux-distributions)). Three of those hosts will be used for `control plane`, three for `workers` and one for `loadbalancer`.

Refer to the [README.md](inventory/README.md) in the `inventory/` directory for details.

### 3. Confirm SSH access and `sudo` rights

Make sure the user specific in the inventory for access via `ssh` can log in using a SSH key, and if necessary add the key to a `ssh-agent`. Confirm the user can `sudo` without requiring a password specified.

> Note: These requirements can be changed by using `ansible_*` variables in the inventory, for example if you must provide a password or if you use something other than `sudo` for elevation.

### 4. Make sure that any packet filtering in place allows necessary traffic.

- The `loadbalancer` node should allow traffic from `anywhere` to `TCP` port `6443`
- All `master` nodes should allow traffic from `loadbalancer` on `TCP` port `6443`
- For sake of simplicity, allow all traffic between `master` and `worker` nodes.

> Note: Open traffic between master and worker nodes may not be ideal in some production scenarios, refer to the [official documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#check-required-ports) for specifics.
 
### 5. Run the playbook

The playbook to run will depend on your Linux Distribution. For CentOS 7 run the following command

```shell
ansible-playbook centos.yaml -i inventory/<name of your inventory>
```

For Ubuntu 18.04 run the following command:

```shell
ansible-playbook ubuntu.yaml -i inventory/<name of your inventory>
```


## Authors

- **Leonardo Murillo** ([@murillodigital](https://github.com/murillodigital))