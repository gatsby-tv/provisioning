# Provisioning

Provisioning scripts for creating a Kubernetes cluster to host GatsbyTV on.

## Definitions

* Provision vs. Deploy - Provisioning is setting up a baseline OS (hardening/package management/configuration) and deploying is setting up a service that can be consumed that is run on provisioned hardware.
* Provisioner - The machine doing the provisioning - ie. a laptop you run the ansible playbooks from.
* Guest - The machine being provisioned - ie. a server you're provisioning for deployment.

## Dependencies

Some of the playbooks require the community.kubernetes ansible collection. This can be installed using `ansible-galaxy collection install community.kubernetes`.

The provisioner machine may need `sshpass` installed. To run the initial setup playbook. Install this on Ubuntu and Debian provisioner machines with `sudo apt install sshpass -y`.

## Usage

**Read through these files before deploying them. Leaving certain fields as is is a security vulnerability.**

Ansible is used for creating the cluster and deploying the services and deployments to it.

- Create a `hosts.ini` file to contain your nodes names and IPs for ansible to use for deployment. `example.hosts.ini` contains an example of what this should look like.

- Run `ansible-playbook -i hosts.ini provision-k8s-host.yml --ask-pass -K` to do initial setup for all the nodes in the cluster (including master).

**Optional ZFS/NFS Section**

Before moving onto the next step, you may want to first configure a server or NAS to handle network file storage. To do this use the following two ansible playbooks to provision a single host to setup ZFS and NFS to share a storage pool over your network.

- Run `ansible-playbook -i hosts.ini provision-zfs-host.yml -K` to setup the ZFS kernel headers and install necessary systemd ZFS services.
- **IMPORTANT:** Manually setup your ZFS pool on the guest machine you ran the last playbook on before continuing.
- Run `ansible-playbook -i hosts.ini provision-nfs-host.yml -K` to share the new ZFS pool with the network.

These scripts will only run for servers in your inventory under the `[nas]` section.

**End Optional ZFS/NFS Section**

- Run `ansible-playbook -i hosts.ini provision-k8s-master-init-cluster.yml -K` to setup the master node and initialize the cluster. Note: This must be done _before_ the worker nodes are configured.

- Run `ansible-playbook -i hosts.ini provision-k8s-worker.yml -K` to setup the worker nodes and add them to the cluster.

- Run `ansible-playbook -i hosts.ini deploy-westegg-staging.yaml -K` to deploy westegg and all the necessary components for staging to the cluster.

## Special Considerations

* These scripts are only designed to work on Debian based systems (specifically 10.7, 10.8, & 10.9).
* `ansible-playbook` version 2.10.x is required.
