# origin3-dev

This repo provides some ansible to bring up an Origin cluster of the specified version from 3.7 to 3.11

### Overview
Useage:
  * Edit options in config.yml
  * ansible-playbook deploy.yml to run

These playbooks will:
  * Setup an OpenShift cluster
  * Install [Service Catalog](https://github.com/kubernetes-incubator/service-catalog)
  * Install [Ansible Service Broker](https://github.com/openshift/ansible-service-broker)

### Local and EC2 deployment options
  * WIP: Set `ec2_install: true` in config.yml to install on ec2

### Known issues
  * Prior to v3.11 nothing is likely to work on Fedora 29+ without a workaround to set the docker cgroup driver to cgroupfs.
    * See https://bugzilla.redhat.com/show_bug.cgi?id=1558425 for more information
