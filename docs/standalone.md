GPU Server Deployment Guide
===

Instructions for deploying standalone GPU servers.

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
