# Using the role

The best place to start is to check the examples project. There is an example playbook for each supported task. The host specific variable are defined in host\_vars.&#x20;

## Getting started

To get started clone the examples repository and install the requirements:

```sh
git clone git@github.com:SlingNode/slingnode-ethereum-examples.git
cd slingnode-ethereum-examples/slingnode.ethereum_node_mgmt
ansible-galaxy install -r requirements.yml
```

The repository contains sample keystores and slashing protection DB. To test the role you can run the examples as they are. If you want to test with your own keystores, edit the host\_vars,  specify the variables in the playbook or crate group\_vars - it is up to the user.&#x20;

The following example Playbooks are at your disposal:

```sh
ansible-playbook import_validator_keys_using_api.yml -i inventory.ini
ansible-playbook import_validator_keys_using_cmd.yml -i inventory.ini
ansible-playbook import_slashing_protection_db.yml -i inventory.ini
ansible-playbook export_slashing_protection_db.yml -i inventory.ini
```
