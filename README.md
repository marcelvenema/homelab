# Homelab - K3S

[![Continuous Integration](https://github.com/zerotouchdeployment/homelab-k3s/workflows/Continuous%20Integration/badge.svg)](https://github.com/zerotouchdeployment/homelab-k3s/actions?query=workflow%3A%22Continuous+Integration%22)
[![Continuous Deployment](https://github.com/zerotouchdeployment/homelab-k3s/workflows/Continuous%20Deployment/badge.svg)](https://github.com/zerotouchdeployment/homelab-k3s/actions?query=workflow%3A%22Continuous+Deployment%22)
[![Coverage Status](https://codecov.io/github/zerotouchdeployment/homelab-k3s/coverage.svg?branch=master)](https://codecov.io/github/zerotouchdeployment/homelab-k3s?branch=master)
[![GitHub release](https://img.shields.io/github/release/zerotouchdeployment/homelab-k3s.svg)](https://github.com/zerotouchdeployment/homelab-k3s/releases/latest)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/zerotouchdeployment/homelab-k3s/blob/master/LICENSE)

## Introduction

Ansible playbook to deploy and configure an HA Kubernetes cluster with k3s, kube-vip, metallb, minio and more.

The goal is a fully automated build of an HA Kubernetes cluster ready for your homelab.

It uses the following core components:
- [kube-vip](https://kube-vip.io/) - Load balancer for the Kubernetes control plane nodes.
- [metallb](https://metallb.universe.tf/) - Load balancer for Kubernetes services.
- [cert-manager](https://cert-manager.io/) - Operator to request SSL certificates and store them as Kubernetes resources.
- [minio](https://min.io) - Distributed block storage system.



## System requirements
Machines:
- For the K3S cluster, use at least three (virtual) machines with x64 or arm64 processor architecture running Debian, Ubuntu or Rocky Linux.
- Machines should have passwordless SSH access. To configure this, see the configuration instructions below.
Ansible control node:
- Packages needed: ansible-core git
- Make sure Ansible version 2.11+ is installed on your control node, the machine you are running the ansible commands. 
- Install required Ansible Galaxy collections this playbook uses by running `sh requirements.sh` on the control node.
- The [`netaddr package`](https://pypi.org/project/netaddr/) must be available to Ansible. See the installation instructions below.

## Getting started

### Requirements
Install OS package dependencies on the control node :
```bash
sudo dnf install ansible-core git
```
Clone the project from github on the control node:
```bash
git clone https://github.com/ZeroTouchDeployment/homelab-k3s.git
```
Install Ansible Galaxy collections on the control node:
```bash
sh requirements.sh
```

### Install
Edit the inventory/homelab/hosts.ini file on the control node to match the system information:
```ini
[master]
192.168.32.90 hostname=k3s-m1.homelab.localdomain

[node]
192.168.32.91 hostname=k3s-w1.homelab.localdomain
192.168.32.92 hostname=k3s-w2.homelab.localdomain

[k3s_cluster:children]
master
node

```
If multiple hosts are in the master group, the playbook will automatically setup k3s in HA mode.

Review and edit the variables in the inventory/homelab/group_vars/all.yaml file.   



Start provisioning the cluster with:
```bash
ansible-playbook install.yaml -i inventory/homelab/hosts.ini
```

### Test
Be sure you can reach the VIP defined in inventory/sample/group_vars/all.yaml as k3s_apiserver_endpoint


Get nodes on the master node:
```bash
kubectl get nodes
```


## Uninstall
To uninstall k3s homelab:
```bash
ansible-playbook uninstall.yaml -i /inventory/homelab/hosts.ini
```


## Configure passwordless SSH access
- Create authentication SSH-Keygen keys on the control node with `ssh-keygen -t rsa`
- Upload key to worker nodes with `ssh-copy-id <user@ip-address of node>`


## Install netaddr package
The Ansible playbook uses the [netaddr package](https://pypi.org/project/netaddr/), this is not default installed with RHEL machines. The netaddr package can be installed with pip: `pip install netaddr --upgrade`
To install pip use `python3.9 -m pip install --upgrade pip`


## Thank you
-
