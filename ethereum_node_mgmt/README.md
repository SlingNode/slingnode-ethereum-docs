# Overview

slingnode.ethereum\_node\_mgmt is an Ansible role that facilitates common management tasks on servers running Ethereum clients. It is designed to seamlessly work with nodes deployed using [slingnode.ethereum](http://localhost:5000/s/Ib9tX0kORM9rMi1DWQvF/) role. &#x20;

The role supports the following tasks:

* validator key import (using command line or Key Manager API)
* Slashing protection DB import and export

The role uses ephemeral containers to carry out command line tasks. The management commands must be executed in the containers with the same configuration (image version, data directories, users, etc) as the Client containers. This is the default for the role and if you don't change any of the paths, you don't need to worry about any of that.&#x20;

## Command line output

Importing keys using command line as well as importing/exporting slashing protection DB tasks execute commands in short lived containers. This poses a challenge knowing what whether the command executed successfully (the container exit status is the same regardles if the command was successfull or not). Implemented logging configuration addresses this issue. Refer to [Logging](logging.md) section.&#x20;

The API based key import task executes directly on the managed host and does not use a container.&#x20;

## Repositories

Ansible Galaxy: [https://galaxy.ansible.com/slingnode/ethereum\_node\_mgmt](https://galaxy.ansible.com/slingnode/ethereum\_node\_mgmt)

The source code: [https://github.com/SlingNode/slingnode-ansible-ethereum\_node\_mgmt](https://github.com/SlingNode/slingnode-ansible-ethereum\_node\_mgmt)

Example playbooks: [https://github.com/SlingNode/slingnode-ethereum-examples](https://github.com/SlingNode/slingnode-ethereum-examples)

## Supported client tasks

The role supports the same clients and Operating Systems as the slingnode.ethereum role. The table below shows supported tasks validator clients.&#x20;

| Task               | Lighthouse | Prysm   | Teku    | Nimbus |
| ------------------ | ---------- | ------- | ------- | ------ |
| Key import         | CMD/API    | CMD/API | CMD/API | API    |
| Slashing DB import | CMD        | CMD     | CMD     | CMD    |
| Slashing DB export | CMD        | CMD     | CMD     | CMD    |

## Requirements

Ansible Docker module is required on the Ansible controller. It can be installed using the below command:

```sh
ansible-galaxy collection install community.docker:==3.4.0
```
