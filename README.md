# origin3-dev
## Overview
This repo provides some ansible to bring up an Origin cluster of the specified version from 3.7 to 3.11. This playbook will set up an Openshift cluster with additional features enabled, such as [Ansible Service Broker](https://github.com/openshift/ansible-service-broker)

## Requirements
  * A recent version of ansible.
    * Ansible 2.7.5+ is known to work
    * Ansible 2.4.0 is known not to work, at least on OS X

## Use
  * copy `config.yml.example` to `config.yml` and edit desired options
  * Set `ec2_install: true` in config.yml to install on ec2 or `ec2_install: false` to install locally
  * For EC2 you will want to set the following environment variables for authentication and region:
    * AWS_ACCESS_KEY_ID
    * AWS_SECRET_ACCESS_KEY
    * AWS_REGION
  * For EC2, as an alternative to setting AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY in the shell environment, they can be put into `~/.aws/credentials` as follows (leaving AWS_REGION as an environment variable):
    ```
    [default]
    aws_access_key_id=<AWS_ACCESS_KEY_ID>
    aws_secret_access_key=<AWS_SECRET_ACCESS_KEY>
    ```
  * For EC2 ensure your private key exists at the location referenced by ec2_private_key_file in config.yml
  * Run `ansible-playbook deploy.yml`

## Instance cleanup
  * `ansible-playbook terminate.yml` will prompt you with a list of instances it will terminate.
    * Responding `yes` will proceed with terminating instances.
    * `no` (or pretty much anything else) should skip termination.
    * It's probably a good idea to verify the instances are yours.
    * Since network resources (VPC, IGW, Security Group, etc.) are shared they are not cleaned up

## Known issues
  * Prior to v3.11 nothing is likely to work on Fedora 29+ without a workaround to set the docker cgroup driver to cgroupfs.
    * This is done by overriding/editing the docker service file and restarting docker
    * See https://bugzilla.redhat.com/show_bug.cgi?id=1558425 for more information

  * Boto on EL7
    * TL;DR Before the first run it may help to remove all boto RPMs with `yum remove python-boto* python2-boto*`
    * The more in depth explanation:
      * boto packages for EC2 support in ansible have proven problematic for some people trying to run on EL7
      * This is because have an outdated python-botocore (1.6.0) in EPEL that does not work with ansible
      * Depending on whether you are using RHEL or CentOS you may see multiple packages for boto and boto3:
        ```
        python-boto.noarch                       2.25.0-2.el7.centos             extras
        python-boto3.noarch                      1.4.6-5.el7                     base
        python2-boto.noarch                      2.45.0-3.el7                    epel
        python2-boto3.noarch                     1.4.6-1.el7                     epel
        ```
      * Some of these rely on an older s3transfer and botocore, while the newer s3transfer includes a bundled botocore that drops the RPM dependency. This breaks some of the above packages.
        ```
        python-s3transfer.noarch                 0.1.13-1.el7.0.1                updates
        python2-s3transfer.noarch                0.1.10-1.el7                    epel
        ```
      * To get a working set of packages we configure a Copr repo with a newer version of botocore and then effectively run
        `yum install python-botocore python-boto python-boto3`, leaving us with the apparently acceptable combination of:
        ```
         python-boto3-1.4.6-5.el7.noarch
         python2-boto-2.45.0-3.el7.noarch
         python2-botocore-1.10.41-4.el7.noarch
        ```
