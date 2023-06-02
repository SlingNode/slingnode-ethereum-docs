# Using the role

The best place to start is to check the examples project. There are multiple example playbooks  ranging from a simple single server deployment to a distributed deployment of large number of nodes. The examples project includes sample playbooks, inventories and group vars that you can adapt to your needs. The examples project is available here [https://github.com/SlingNode/slingnode-ethereum-examples](https://github.com/SlingNode/slingnode-ethereum-examples)

Each of the examples includes:

* Playbook
* Variables (inline in the playbook or in group\_vars)
* Inventory

To use the example playbook you only need to update the inventory to match your hosts.

## Getting started

To get started clone the examples repository and install the requirements:

```sh
git clone git@github.com:SlingNode/slingnode-ethereum-examples.git
cd slingnode-ethereum-examples/slingnode.ethereum
ansible-galaxy install -r requirements.yml
```

## Single server deployments

This section outlines how to deploy all three layers or any combination of them on the same server.  In these scenarios execution / consensus / validator run on the same server and communicate over the internal Docker network.&#x20;

A high-level end state is:

* peer-2-peer ports are exposed to the host and accessible from remote nodes
* Execution client's JSON RPC API is exposed to the Docker network and not accessible from the host
* Consensus client's beacon API is exposed to the Docker network and not accessible from the host
* Validator API is not enabled (default behavior)&#x20;
* Metrics are enabled for all layers, exposed to the Docker network and not accessible from the host (Prometheus must be running on the same host and be connected to the same Docker network - check out [slingnode.ethereum\_observability](https://slingnode.gitbook.io/slingnode.ethereum\_observability/) role)
* Shared JWT secret is generated automatically see [JWT secret](jwt-secret.md) for more details

### Whole stack per node

In this example all three layers (execution, consensus, validator) are deployed per server. It can be a single or multiple servers. The only two variables you will want to modify are:

```yaml
  vars:
    suggested_fee_recipient: "0xa10214731A6D9eC03d36d1437796D1cEe6a061f7"
    graffiti: SlingNode.com
```

#### deploy-whole-stack-per-node-default-clients.yml&#x20;

This example will deploy the default clients which are Geth and Lighthouse (consensus and validator).&#x20;

```sh
cd slingnode-ethereum-examples/slingnode.ethereum/whole-stack-per-node
ansible-playbook deploy-whole-stack-per-node-default-clients.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```

#### deploy-whole-stack-per-node-specify-clients.yml

This example will deploy Nethermind and Prysm. To specify the desired client mix, you can modify the "clients" variable:

```yaml
    clients:
      execution: nethermind
      consensus: prysm
      validator: prysm
```

To run the the playbook:&#x20;

```sh
cd slingnode-ethereum-examples/slingnode.ethereum/whole-stack-per-node
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
cd slingnode-ethereum-examples/slingnode.ethereum/execution-and-consensus-only
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
cd slingnode-ethereum-examples/slingnode.ethereum/execution-and-consensus-only
ansible-playbook deploy-execution-and-consensus-only-specify-clients.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```

## Distributed deployments

This section outlines how to mange split deployments where execution / consensus / validator  run on separate servers or for example execution and consensus run on the same server and validator (or multiple validators) run on separate servers.&#x20;

In these scenarios the clients communicate over the LAN, the API ports are exposed to the host and accessible from anywhere. <mark style="color:red;">This kind of deployment is suitable only when your hosts are protected by a network firewall or security groups</mark>.&#x20;

Note: there are multiple ways of achieving similar results, the implementation depends on the goal you want to achieve. This is an example only and it should be adapted to your needs.&#x20;

In split deployments we need to enable the clients to communicate with one another over the LAN, this means that we need to expose the required API ports to the host.&#x20;

Furthermore we need to dynamically configure each layer to communicate with required servers.&#x20;

A high-level end state is:

* peer-2-peer ports are exposed to the host and accessible from remote nodes
* Execution client's JSON RPC API is exposed to the host and accessible from remote hosts
* Consensus client's beacon API is exposed to the host and accessible from remote hosts
* Validator API is not enabled (default behavior - check [Enabling Validator client API](enabling-validator-client-api.md) section for details)&#x20;
* Metrics are enabled for all layers, exposed to the host and accessible from remote hosts
* Each layer is dynamically configured to connect to the required nodes (for example validator-01 -> consensus-01 -> execution-01)

### Each layer on a separate server (same clients)

In this example we deploy each layer on a separate server. The same clients will be deployed to all servers included in the play.  The inventory is structured so that we can use group\_vars to achieve the desired state.&#x20;

The inventory:

```ini
[clients_execution]
slingnode-exe-[01:02]

[clients_consensus]
slingnode-con-[01:02]

[clients_validator]
slingnode-val-[01:02]

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

Check the [repository](https://github.com/SlingNode/slingnode-ethereum-examples/tree/master/slingnode.ethereum/each-layer-on-a-seprate-server-same-clients/group\_vars) to see the exact variables.

To run the example playbook:

```sh
cd slingnode-ethereum-examples/slingnode.ethereum/each-layer-on-a-seprate-server-same-clients
ansible-playbook deploy-each-layer-on-a-seprate-server-same-clients.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```

### Each layer on a separate server (different clients)

In this example we deploy each layer on a separate server.  In this scenario the role deploys one type of the client to the first 25 servers in each host group and a different client to the remaining 25 servers in each host group.

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

Check the [repository](https://github.com/SlingNode/slingnode-ethereum-examples/tree/master/slingnode.ethereum/each-layer-on-a-seprate-server-different-clients/group\_vars) to see the exact variables.

To run the example playbook:

```sh
cd slingnode-ethereum-examples/slingnode.ethereum/each-layer-on-a-seprate-server-different-clients/
ansible-playbook deploy-each-layer-on-a-seprate-server-different-clients.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```
