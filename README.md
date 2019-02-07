# origin3-dev
## Overview
This repo provides some ansible to bring up an Origin cluster of the specified version from 3.7 to 3.11. This playbook will set up an Openshift cluster with additional features enabled, such as [Ansible Service Broker](https://github.com/openshift/ansible-service-broker)

## Use
  * copy `config.yml.example` to `config.yml` and edit desired options
  * Set `ec2_install: true` in config.yml to install on ec2 or `ec2_install: false` to install locally
  * For EC2 you will want to set the following environment variables for authentication:
    * AWS_ACCESS_KEY_ID
    * AWS_SECRET_ACCESS_KEY
  * For EC2 you'll also want to make sure your public key exists at `~/.ssh/${ec2_key}.pem`
  * Run `ansible-playbook deploy.yml`

## Known issues
  * Prior to v3.11 nothing is likely to work on Fedora 29+ without a workaround to set the docker cgroup driver to cgroupfs.
    * This is done by overriding/editing the docker service file and restarting docker
    * See https://bugzilla.redhat.com/show_bug.cgi?id=1558425 for more information
