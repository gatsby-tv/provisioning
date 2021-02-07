# Provisioning

Provisioning scripts for creating a Kubernetes cluster to host GatsbyTV on.

## Usage

**Read through these files before deploying them. Leaving certain fields as is is a security vulnerability.**

Ansible is used for creating the cluster and deploying the services and deployments to it.

- Create a `hosts.ini` file to contain your nodes names and IPs for ansible to use for deployment. `host.ini.example` contains an example of what this should look like.

- Run `ansible-playbook -i hosts.ini provision-k8s-host.yml --ask-pass -K` to do initial setup for all the nodes in the cluster (including master).

- Run `ansible-playbook -i hosts.ini provision-k8s-master-init-cluster.yml --ask-pass -K` to setup the master node and initialize the cluster. Note: This must be done _before_ the worker nodes are configured.

- Run `ansible-playbook -i hosts.ini provision-k8s-worker.yml --ask-pass -K` to setup the worker nodes and add them to the cluster.

## Special Considerations

These scripts are only designed to work on Debian based systems (specifically 10.7).
