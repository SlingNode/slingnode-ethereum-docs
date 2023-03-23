---
description: This page describes the the main variables.
---

# Role Variables

Role variables are defined in 'defaults'. This means they have the lowest precedence and can be easily overridden. See [Ansible documentation](https://docs.ansible.com/ansible/latest/playbook\_guide/playbooks\_variables.html#understanding-variable-precedence) for details on the precedence.&#x20;

All client specific variables are defined in their corresponding variable file. All client specific variables have unique names (prefixed with clientname\_) so there's no risk of a clash.&#x20;

| Variable location              | Purpose                                                           |
| ------------------------------ | ----------------------------------------------------------------- |
| defaults/main/main.yml         | generic variables for the role;  common variables for all clients |
| defaults/main/\{{client\}}.yml | variables specific to the client                                  |
| vars/\{{os\_family\}}.yml      | variables specific to the OS                                      |

### Important variables

This section outlines variables that you will most likely want to modify.

#### clients

"clients" variable defines what clients will be deployed. It defaults to geth and lighthouse. Possible values are:&#x20;

* execution: geth / nethermind / erigon / besu
* consensus and validator: lighthouse, prysm, teku, nimbus

```yaml
clients:
  execution: geth
  consensus: lighthouse
  validator: lighthouse
```

#### deploy\_execution / deploy\_consensus / deploy\_validator

These three variables define which layer of the stack will be deployed. By default all three layers are deployed. Check out [examples](using-the-role.md) to see how they can be used.

```yaml
deploy_execution: true
deploy_consensus: true
deploy_validator: true
```

#### enable\_firewall

By default set to true, the role will make sure the firewall package is installed and will configure rules for the client that is being deployed.&#x20;

```yaml
enable_firewall: true
```

This is meant to be a safe default for anyone who uses the role to deploy on a server that is directly exposed to the internet (for example an OVH bare metal server). It is expected that for more enterprise type of deployments this would be set to false. Refer to [Host firewall](architecture/security.md#host-firewall) section for details and notes on SSH port.&#x20;

#### network

Specifies Ethereum network to connect to.&#x20;

```yaml
network: goerli
```

#### consensus\_checkpoint\_sync\_enabled

Enable/disable consensus client checkpoint sync feature. Defaults to "true" (sync from checkpoint).

```yaml
consensus_checkpoint_sync_enabled: true
```

Refer to [Checkpoint sync](checkpoint-sync.md) section for details.

#### consensus\_checkpoint\_sync\_url

Beacon endpoint to sync from. This needs to match the network you are deploying on -  [network](role-variables.md#network) variable.  Defaults to Goerli.

```yaml
consensus_checkpoint_sync_url: https://sync-goerli.beaconcha.in
```

Refer to [Checkpoint sync](checkpoint-sync.md) section for details.

#### suggested\_fee\_recipient

A priority fee recipient address on your validator client instance and consensus node.

```yaml
suggested_fee_recipient: "0xa10214731A6D9eC03d36d1437796D1cEe6a061f7" 
```

#### graffiti

```yaml
graffiti: SlingNode.com
```

