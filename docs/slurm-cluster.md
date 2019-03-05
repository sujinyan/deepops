Slurm Deployment Guide
===

Instructions for deploying a GPU cluster with Slurm

## Requirements

  * Control system to run the install process
  * One server to act as the Slurm controller/login node
  * One or more servers to act as the Slurm compute nodes
  * (Optional) Management server (if installing OS via PXE)

## Installation Instructions
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
