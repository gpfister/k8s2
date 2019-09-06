# k82s (Kubernetes Cluster 2)

Stand up a Ubuntu 18.04 based Kubernetes cluster with Ansible

# Prerequisites

## Hardware

* Ubuntu 18.04 (amd64) LTS Server machine (virtual or physical)
* Network connection (wireless or wired) with access to the internet

## Software

* [Ubuntu 18.04.3 LTS Server](https://ubuntu.com/download/server/thank-you?country=FR&version=18.04.3&architecture=amd64) (installed on a physical of virtual machine)

* Each server should have a static IP
    * Requirement for Kubernetes and Ansible inventory
    * You can set these via OS configuration or DHCP reservations (your choice)

* Swap files must be disable on each server

* Ability to SSH into all servers and escalate privileges with sudo

* [Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html) 2.2 or higher

* [`kubectl`](https://kubernetes.io/docs/tasks/tools/install-kubectl/) should be available on the system you intend to use to interact with the Kubernetes cluster.
    * If you are going to login to one of the Raspberry Pis to interact with the cluster `kubectl` is installed and configured by default on the master Kubernetes master.
    * If you are administering the cluster from a remote machine (your laptop, desktop, server, bastion host, etc.) `kubectl` will not be installed on the remote machine but it will be configured to interact with the newly built cluster once `kubectl` is installed.

## Recommendations

* Setup SSH key pairs so your password is not required every time Ansible runs

# Stand Up Your Kubernetes Cluster

## Download the latest release or clone the repo:

```
git clone https://github.com/gpfister/k8s2.git
```

## Modify ansible.cfg and inventory

Modify the `inventory` file to suit your environment. Change the names to your liking and the IPs to the addresses of your Raspberry Pis.

If your SSH user on the servers is not the `root` user modify `remote_user` in the `ansible.cfg`.

## Confirm Ansible is working with your Raspberry Pis:

```
ansible -m ping all
```
This may fail to ping if you have not setup SSH keys and only configured your Pi's with passwords

## Deploy, Deploy, Deploy

```
ansible-playbook cluster.yml
```

# Interact with Kubernetes

## CLI

Test your Kubernetes cluster is up and running:

```
kubectl get nodes
```

The output should look something like this:

```
NAME       STATUS    ROLES     AGE       VERSION
k8s2m01    Ready     master    2d        v1.9.1
k8s2m01    Ready     <none>    2d        v1.9.1
k8s2s02    Ready     <none>    2d        v1.9.1
```

## Dashboard

k8s2 installs the non-HTTPS version of the Kubernetes dashboard. This is not recommended for production clusters but, it simplifies the setup. Access the dashboard by running:

```
kubectl proxy
```

Then open a web browser and navigate to:
[http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)

# Need to Start Over?

Did something go wrong? Nodes fail some process or not joined to the cluster? Break Docker Versions with apt-update?

Try the process again from the beginning:

```
ansible-playbook cleanup.yml
```
Wait for everything to run and then start again with:

```
ansible-playbook cluster.yml
```

# References & Credits

These playbooks were assembled using a handful of very helpful guides:

* [Installing kubeadm](https://kubernetes.io/docs/setup/independent/install-kubeadm/)
* [kubernetes/dashboard - Access control - Admin privileges](https://github.com/kubernetes/dashboard/wiki/Access-control#admin-privileges)
* [Install using the convenience script](https://docs.docker.com/engine/installation/linux/docker-ce/debian/#install-using-the-convenience-script)

A very special thanks to [rak8s](https://rak8s.io) for inspiring it.
