# Changing Validator clients

As an example we will migrate from Prysm to Teku running Nethermind execution client.  Our starting state is:

```yaml
vars:
  clients:
    execution: nethermind
    consensus: prysm 

  deploy_execution: true
  deploy_consensus: true
  deploy_validator: true
```

The higl-level steps are:

1.  Export slashing protection db - validator\_mgmt\_export\_slashing\_protection\_db.yml                  `vars:`&#x20;

    &#x20;  `clients: consensus: prysm`
2. Deploy desired client - deploy\_blockchain.yml vars: clients: execution: nethermind consensus: teku
3. Import slashing protection db - validator\_mgmt\_import\_slashing\_protection\_db.yml    vars: clients: consensus: teku
4. Import validator keys - validator\_mgmt\_import\_validator\_keys.yml - vars: clients: consensus: teku

### 1. Export slashing protection db

In the first step we export the slashing protection DB from the existing validator client.  See [https://docs.prylabs.network/docs/wallet/slashing-protection](https://docs.prylabs.network/docs/wallet/slashing-protection) for explanation on what it is and why it is important.  This playbook will execute the following tasks:&#x20;

1. Create required dirs&#x20;
2. Copy docker-compose file&#x20;
3. Stop validator container
4. Delete slashing protection DB dump if it exists
5. Export slashing protection db

The exact tasks can be examined in the following files:

* validator\_management/tasks/main.yml
* validator\_management/tasks/export\_slashing\_protection\_db.yml

There are two variables that we need to set as shown in the below example playbook (validator\_mgmt\_export\_slashing\_protection\_db.yml in the examples project)

```yaml
---
- name: Export slashing protection db
  hosts: testservers
  become: true

  vars:
    clients:
      consensus: prysm

    export_slashing_protection_db: true

  roles:
    - role: validator_management

```

In this step variable "clients.consensus" defines the **current** validator client that we are exporting the DB from. Variable "export\_slashing\_protection\_db" defines the action we want the role to perform. &#x20;

The following messages should be logged by Prysm upon successful export:

```json
Running on the Prater Testnet
Writing slashing protection export JSON file to /slashing_protection_export/slashing_protection_db.json/slashing_protection.json
Extracting signed attestations by validator public key 100% [[32m=[0m[32m=[0m[32m=[0m[32m=[0m[32m=[0m[32m=[0m[32m=[0m[32m=[0m[32m=[0m] [0s:0s][0m
Successfully wrote /slashing_protection_export/slashing_protection_db.json/slashing_protection.json. You can import this file using Prysm's validator slashing-protection-history import command in another machine
```

Using SlingNode's observability setup we can search the relevant logs using the following tags:

layer: validator\_management

By default the DB is saved in the following directory on the host server:

```shell-session
sudo ls -la /opt/blockchain/validator/slashing_protection_export/
-rwx------ 1 validator_client validator_client 196205 Feb 13 09:21 slashing_protection.json
```

### 2.  Deploy desired client

We are ready to deploy the new consensus and validator client.&#x20;

For details on using this role  see -> TODO

```yaml
---
- name: Deploy blockchain stack
  hosts: testservers
  become: true

  vars:
    clients:
      execution: nethermind
      consensus: teku 

    deploy_execution: true
    deploy_consensus: true
    deploy_validator: true

    suggested_fee_recipient: "0xa10214731A6D9eC03d36d1437796D1cEe6a061f7"
    graffiti: SlingNode.com

  roles:
    - role: blockchain
      tags:
        - blockchain

```

### 3. Import slashing protection db

We are ready to import slashing protection db exported in step 1.  In order to do that we can use validator\_mgmt\_import\_slashing\_protection\_db.yml playbook provided in the examples project. This playbook will execute the following tasks:

1. Create required dirs&#x20;
2. Copy docker-compose file
3. Import slashing protection db

The exact tasks can be examined in the following files:

* validator\_management/tasks/main.yml
* validator\_management/tasks/import\_slashing\_protection\_db.yml

There are two variables that we need to set as shown in the below example playbook (validator\_mgmt\_export\_slashing\_protection\_db.yml in the examples project)

```yaml
- name: Import slashing protection db
  hosts: testservers
  become: true

  vars:
    clients:
      consensus: teku

    import_slashing_protection_db: true

  roles:
    - role: validator_management
```

In this step variable "clients.consensus" defines the **new** validator client that we are importing the DB to. Variable "import\_slashing\_protection\_db" defines the action we want the role to perform. &#x20;

The following messages should be logged by Teku upon successful export:

```
Reading slashing protection data from: /slashing_protection_export/slashing_protection.json
Writing slashing protection data to: /var/lib/teku/validator/slashprotection
Importing 876a9a7fadb5b9d2114a5180f9fe50b451cbab5f241b42e476b724a3575e5a8277767bc5a7c831c63f066a9a725c53d6
Updated 1 validator slashing protection records
```

Using SlingNode's observability setup we can search the relevant logs using the following tags:

layer: validator\_management

### 4. Import validator keys

We have successfully imported the slashing protection DB and now we can safely import the keys to in the validator.&#x20;

In order to do that we can use validator\_mgmt\_import\_validator\_keys.yml playbook provided in the examples project. The exact steps performed by this playbook vary based on the client. Each client uses slightly different mechanism to import the keys. For example Teku will automatically load keystore and corresponsing password from directories specified using --validator-keys flag.  See Teku's documenation for details [https://docs.teku.consensys.net/Reference/CLI/CLI-Syntax#validator-keys](https://docs.teku.consensys.net/Reference/CLI/CLI-Syntax#validator-keys)

The exact tasks can be examined in the following files:

* validator\_management/tasks/main.yml
* validator\_management/tasks/import\_validator\_keys.yml

There are three variables that we need to set as shown in the below example playbook (validator\_mgmt\_import\_validator\_keys.yml in the examples project)

```yaml
---
- name: Import validator keys
  hosts: testservers
  become: true

  vars:
    clients:
      consensus: teku

    import_validator_keys: true

    keystore_files:
      - src: validator_keys/keystore-m_12381_3600_0_0_0-1672826772.json
        dest: "{{ blockchain_root_path }}/validator/keystore/keystore-m_12381_3600_0_0_0-1672826772.json"
      - src: validator_keys/keystore-m_12381_3600_0_0_0-1672826772.txt
        dest: "{{ blockchain_root_path }}/validator/keystore/keystore-m_12381_3600_0_0_0-1672826772.txt"

  roles:
    - role: validator_managementam
    
```



The following messages should be logged by Teku upon copying the keystore and password:

```
Loading 1 validator keys...
Loaded 1 Validators: 876a9a7
```

At this point the validator should start publishing attestations. We can verify in the logs.

```
Validator *** Published attestation Count: 1, Slot: 4981087, Root: 17e113462f9bf5f6cd304032bfd1b5af522ce24260abfd5cd665f884bdec8db9
```

Using SlingNode's observability setup we can search the relevant logs using the following tags:

layer: validator
