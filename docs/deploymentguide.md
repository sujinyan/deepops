Deployment Guide
===


## Contents

* [Overview](#overview)
* [Kubernetes Deployment](#kubernetes-deployment)
  * [Requirements](#kubernetes-requirements)
  * [Installation Steps](#kubernetes-installation)
  * [Additional Information](#kubernetes-additional)
  * [Optional Steps](#kubernetes-optional)
* [Slurm Deployment](#slurm-deployment)
  * [Requirements](#slurm-requirements)
  * [Installation Steps](#slurm-installation)
* [Standalone Deployment](#standalone-deployment)
  * [Installation Steps](#standalone-installation)

## Overview

__general blurb goes here__

## Kubernetes Deployment

Instructions for deploying a GPU cluster with Kubernetes.

### Requirements <a name=kubernetes-requirements></a>

  * Control system to run the install process
  * One or more servers on which to install Kubernetes
  * (Optional) Management server (if installing OS via PXE)

### Installation Steps <a name=kubernetes-installation></a>

1. Install a supported operating system.

   Install a supported operating system on all servers via a third-party solution 
   (i.e. [MAAS](https://maas.io/), [Foreman](https://www.theforeman.org/)) or 
   utilize the provided [OS install container](PXE.md).

2. Setup a control machine.

   ```sh
   # Install software prerequisites and copy default configuration
   ./scripts/setup.sh
   ```

3. Create a server inventory.

   ```sh
   # Specify IP addresses of Kubernetes nodes
   ./scripts/k8s_inventory.sh 10.0.0.1 10.0.0.2 10.0.0.3

   # (optional) Modify `k8s-config/hosts.ini` to configure hosts for specific roles
   # 	     Make sure the [etcd] group has an odd number of hosts
   ```

4. Install Kubernetes.

   Wordage about [Ansible](ANSIBLE.md) documentation can perhaps go here.

   ```sh
   # NOTE: If SSH requires a password, add: `-k`
   # NOTE: If sudo on remote machine requires a password, add: `-K`
   # NOTE: If SSH user is different than current user, add: `-u ubuntu`
   ansible-playbook -i k8s-config/hosts.ini -b playbooks/k8s-cluster.yml
   ```

5. Verify that the Kubernetes cluster is working.

   ```sh
   # You may need to manually run: `sudo cp ./k8s-config/artifacts/kubectl /usr/local/bin`
   kubectl get nodes
   ```

   ```sh
   kubectl run gpu-test --rm -t -i --restart=Never --image=nvidia/cuda --limits=nvidia.com/gpu=1 -- nvidia-smi
   ```

### Optional Steps <a name=kubernetes-optional></a>

1. Install the Kubernetes dashboard.

   Run the script to create an administrative user and print out the dashboard URL and access token:

   ```sh
   ./scripts/k8s_deploy_dashboard_user.sh
   ```

2. Setup presistent storage.

   Ceph cluster running on Kubernetes to provide persistent storage

   ```sh
   ./scripts/k8s_deploy_rook.sh
   ```

3. Setup monitoring.

   Prometheus and Grafana to monitor Kubernetes and cluster nodes

   ```sh
   ./scripts/k8s_deploy_monitoring.sh
   ```

4. Setup the container registry.

    The default container registry hostname is `registry.local`. To set another hostname (for example,
    one that is resolvable outside the cluster), add `-e container_registry_hostname=registry.example.com`.

    ```sh
    ansible-playbook -i k8s-config/hosts.ini -b --tags container-registry playbooks/k8s-services.yml
    ```

### Additional Information <a name=kubernetes-additional></a>

More information on Kubespray can be found in the official [Getting Started Guide](https://github.com/kubernetes-sigs/kubespray/blob/master/docs/getting-started.md)

## Slurm Deployment <a name=slurm-deployment></a>

Instructions for deploying a GPU cluster with Slurm

### Requirements <a name=slurm-requirements></a>

  * Control system to run the install process
  * One server to act as the Slurm controller/login node
  * One or more servers to act as the Slurm compute nodes
  * (Optional) Management server (if installing OS via PXE)

### Installation Steps <a name=slurm-installation></a>

1. Install the Operating System

   Install a supported operating system on all servers via a third-party solution 
   (i.e. [MAAS](https://maas.io/), [Foreman](https://www.theforeman.org/)) or 
   utilize the provided [OS install container](PXE.md).

2. Configure the System

   _Set up control machine_

   ```sh
   # Install software prerequisites and copy default configuration
   ./scripts/setup.sh
   ```

   _Edit server inventory and configuration_

   ```sh
   # Edit inventory
   # Add Slurm controller/login host to `login` group
   # Add Slurm worker/compute hosts to `gpu-servers` or `dgx-servers` groups
   vi config/inventory

   # (optional) Modify `config/group_vars/*.yml` to set configuration parameters
   ```

3. Install Slurm

   ```sh
   # NOTE: If SSH requires a password, add: `-k`
   # NOTE: If sudo on remote machine requires a password, add: `-K`
   # NOTE: If SSH user is different than current user, add: `-u ubuntu`
   ansible-playbook -l slurm-cluster playbooks/slurm-cluster.yml
   ```

## Standalone Deployment <a name=standalone-deployment></a>

Instructions for deploying standalone GPU servers.

### Installation Steps  <a name=standalone-installation></a>

The install process should be run from a separate control system since
GPU driver installation may trigger a reboot.

1. Set-up the control machine

   ```sh
   # Install software prerequisites and copy default configuration
   ./scripts/setup.sh
   ```

2. Edit the server inventory and configuration

   ```sh
   # Edit inventory
   # Add GPU servers to `gpu-servers` group
   vi config/inventory

   # (optional) Modify `config/group_vars/*.yml` to set configuration parameters
   ```

3. Install Ansible.

   ```sh
   # NOTE: If SSH requires a password, add: `-k`
   # NOTE: If sudo on remote machine requires a password, add: `-K`
   # NOTE: If SSH user is different than current user, add: `-u ubuntu`
   ansible-playbook -l gpu-servers playbooks/standalone.yml
   ```
