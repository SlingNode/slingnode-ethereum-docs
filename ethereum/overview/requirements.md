# Requirements

## Ansible controller

Ansible Docker module is required on the Ansible controller. It can be installed using the below command:

```sh
ansible-galaxy collection install community.docker:==3.4.0
```

## Managed host

The role requires a running docker daemon and docker compose plugin installed on the target server. The role has been tested with [https://galaxy.ansible.com/geerlingguy/docker](https://galaxy.ansible.com/geerlingguy/docker). See examples for details.&#x20;

Please note that geerlingguy.docker role does not work on AmazonLinux2.&#x20;
