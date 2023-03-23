---
description: This page shows examples of how to use the role
---

# Using the role

The best place to start is to check the examples project. There are multiple example playbooks and ranging from a simple single node deployment to a distributed deployment of large number of nodes. The examples project includes sample playbooks, inventories and group vars that you can adapt to your needs. The examples project is available here [https://github.com/SlingNode/slingnode-ethereum-examples](https://github.com/SlingNode/slingnode-ethereum-examples)



Each of the examples includes:

* Playbook
* Variables required for example (inline in the playbook or in group\_vars)
* Inventory

To use any of the examples you only need to update the inventory and it all should just work :relaxed:&#x20;



add steps to get the examples

## Single tier deployments

This section outlines how to deploy the all three layers or any combination of them on the same server.  In these scenarios execution / consensus / validator run on the same server and communicate over the internal Docker network.&#x20;

A high-level end state is:

* peer-2-peer ports are exposed to the host and accessible from remote nodes
* Execution client's HTTP RPC API is exposed to the Docker network and not accessible from the host
* Consensus client's beacon API is exposed to the Docker network and not accessible from the host
* Validator API is not enabled (default behavior)&#x20;
* Metrics are enabled for all layers, exposed to the Docker network and not accessible from the host (Prometheus must be running on the same host and be connected to the same Docker network - check out slingnode.ehtereum\_observability role)
* Shared JWT secret is generated automatically see [JWT secret](jwt-secret.md) for more details

### Whole stack per node

In this example all three layers (execution, consensus, validator) are deployed per server. This can be a single or multiple servers. The only two you may want to modify are:

```yaml
  vars:
    suggested_fee_recipient: "0xa10214731A6D9eC03d36d1437796D1cEe6a061f7"
    graffiti: SlingNode.com
```

#### deploy-whole-stack-per-node-default-clients.yml&#x20;

This example will deploy the default clients which are Geth and Lighthouse (consensus and validator).&#x20;

```sh
cd ethereum-examples/slingnode.ethereum/whole-stack-per-node
ansible-playbook deploy-whole-stack-per-node-default-clients.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```

#### deploy-whole-stack-per-node-specify-clients.yml

This example will deploy Nethermind and Prysm. To specify the desired client mix, you can modify the "clients" var:

```yaml
    clients:
      execution: nethermind
      consensus: prysm
      validator: prysm
```

To run the the playbook:&#x20;

```sh
cd ethereum-examples/slingnode.ethereum/whole-stack-per-node
ansible-playbook deploy-whole-stack-per-node-specify-clients.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```

### Execution and consensus only

In this example only execution and consensus layer are deployed. Both on the same server. This can be a single or multiple servers. There are three variables that control which layer gets deployed by the role.  With the values below the role will deploy execution and consensus but not the validator.  You can toggle the values to achieve the required behavior. The role can deploy any combination.&#x20;

```yaml
  vars:
    deploy_execution: true
    deploy_consensus: true
    deploy_validator: false
```

#### deploy-execution-and-consensus-only-default-clients.yml

This example will deploy the default clients which are Geth and Lighthouse.&#x20;

```sh
cd ethereum-examples/slingnode.ethereum/execution-and-consensus-only
ansible-playbook deploy-execution-and-consensus-only-default-clients.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```

#### deploy-execution-and-consensus-only-specify-clients.yml

This example will deploy Nethermind and Prysm (consensus only). To achieve this behavior the variables look as follows:

```yaml
  vars:
    clients:
      execution: nethermind
      consensus: prysm
    
    deploy_execution: true
    deploy_consensus: true
    deploy_validator: false
```

To run the sample playbook:

```sh
cd ethereum-examples/slingnode.ethereum/execution-and-consensus-only
ansible-playbook deploy-execution-and-consensus-only-specify-clients.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```

## Multi tier deployments

This section outlines how to mange split deployments where execution / consensus / validator all run on separate servers or for example execution and consensus run and the same server and validator (or multiple validators) run on separate servers.&#x20;

&#x20;In these scenarios the clients communicate over the LAN, the API ports are exposed to the host and accessible from anywhere. This kind of deployments is suitable only when your hosts are protected by a network firewall or security groups.&#x20;

Note: there are multiple ways of achieving similar results, the implementation depends on the goal you want to achieve. This is an example only and it should be adapted to your needs.&#x20;

In split deployments we need to enable the clients to communicate with one another over the LAN, this means we need to expose the required API ports to the host.&#x20;

Furthermore we need to dynamically configure each layer to communicate with required servers.&#x20;

A high-level end state is:

* peer-2-peer ports are exposed to the host and accessible from remote nodes
* Execution client's HTTP RPC API is exposed to the host and accessible from remote hosts
* Consensus client's beacon API is exposed to the host and accessible from remote hosts
* Validator API is not enabled (default behavior)&#x20;
* Metrics are enabled for all layers, exposed to the Docker network and not accessible from the host
* validator-server-01 is configured to connect to consensus-server-01&#x20;
* consensus-server-01 is configured to connect to execution-server-01



### Each layer on a separate server (same clients)

In this example we deploy each layer on a separate server. The same clients will be deployed to all servers included in the play.  The inventory is structured so that we can user group\_vars to achieve the desired state.&#x20;

The inventory:

```ini
[clients_execution]
slingnode-exe-[01:50]

[clients_consensus]
slingnode-con-[01:50]

[clients_validator]
slingnode-val-[01:50]

[ethereum_servers:children]
clients_execution
clients_consensus
clients_validator
```

group\_vars:

```sh
group_vars/
├── clients_consensus.yml
├── clients_execution.yml
└── clients_validator.yml
```

See the exact variables in here.&#x20;

To run the example playbook:

```
cd ethereum-examples/slingnode.ethereum/each-layer-on-a-seprate-server-same-clients
ansible-playbook deploy-each-layer-on-a-seprate-server-same-clients.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```





### Each layer on a separate server (different clients)

In this example we deploy each layer on a separate server.  In this scenario the role deploys one type of client to the first 25 servers in each host group and a different client to the remaining 25 servers in each host group.

The inventory is structured so that we can user group\_vars to achieve the desired state.&#x20;

The inventory:

```ini
[execution_geth]
slingnode-exe-[01:25]

[execution_nethermind]
slingnode-exe-[26:50]


[consensus_lighthouse]
slingnode-con-[01:25]

[consensus_prysm]
slingnode-con-[26:50]


[validator_lighthouse]
slingnode-val-[01:25]

[validator_prysm]
slingnode-val-[26:50]


[clients_execution:children]
execution_geth
execution_nethermind

[clients_consensus:children]
consensus_lighthouse
consensus_prysm

[clients_validator:children]
validator_lighthouse
validator_prysm


[ethereum_servers:children]
clients_execution
clients_consensus
clients_validator
```

group\_vars:

```sh
group_vars/
├── clients_consensus.yml
├── clients_execution.yml
├── clients_validator.yml
├── consensus_lighthouse.yml
├── consensus_prysm.yml
├── execution_geth.yml
├── execution_nethermind.yml
├── validator_lighthouse.yml
└── validator_prysm.yml
```

See the exact variables in here.&#x20;
