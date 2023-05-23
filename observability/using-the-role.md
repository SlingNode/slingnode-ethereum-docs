# Using the role

The best place to start is to check the examples project. There is an example playbook that cover different deployment types.

## Getting started

To get started clone the examples repository and install the requirements:

```sh
git clone git@github.com:SlingNode/slingnode-ethereum-examples.git
cd slingnode-ethereum-examples/slingnode.ethereum
ansible-galaxy install -r requirements.yml
```

## Single tier deployments

This section outlines how to deploy all required component to monitor the ethereum clients which have been deployed following the [single tier deployment](http://docs.slingnode.com/slingnode.ethereum/using-the-role#single-tier-deployments).
In ths scenario execution / consensus / validator run on the same server and communicate over the internal Docker network. Observability components and agent runs and communicate with the client also over the internal Docker network.

A high-level end state is:
* grafana, elk exposed to the host
* filebeat, node-exporter, ethereum-metrics-exporter, cadvisor exposed to the internal docker network

#### single-node-deployment:

This example will deploy all the observability components on a single server where ethereum clients are also deployed;

```sh
cd slingnode-ethereum-examples/slingnode.ethereum_observability/single-node-deployment
ansible-playbook single-node-deployment.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```

No variables are required for this scenario to work, unless there is specific requirements.

## Multi tier deployments

This section outlines how to mange split deployments where execution / consensus / validator  run on separate servers or for example execution and consensus run on the same server and validator (or multiple validators) run on separate servers. In this case we will have the observability component deployed in a dedicated server and agents deployed across all the ethereum clients servers

In these scenarios the components communicate over the LAN, the agents ports are exposed to the host and accessible from anywhere. <mark style="color:red;">This kind of deployment is suitable only when your hosts are protected by a network firewall or security groups</mark>.

Note: there are multiple ways of achieving similar results, the implementation depends on the goal you want to achieve. This is an example only and it should be adapted to your needs.

A high-level end state is:
* grafana, elk exposed to the host on a dedicated server
* filebeat, node-exporter, ethereum-metrics-exporter, cadvisor exposed to the host where the clients are running alongside.


### distributed-deployment-layer-per-server:

In this example each layer is deployed on a separate server.  In this scenario one type of the client to the first 25 servers in each host group and a different client to the remaining 25 servers in each host group.

The observability components, grafana and elk, are deployed into a dedicates server under the group `monitoring_server`, and agents will be deployed across all the ethereum servers.

In this case there are some variables that are required to be defined:

group_vars/all.yml
```yaml
monitoring_server_host: "{{ hostvars[groups['monitoring_server'][0]]['ansible_facts']['default_ipv4']['address'] }}"
single_server_deployment: false
```

group_vars/ethereum_servers.yml
```yaml
monitoring_server: false
install_monitoring_agents: true
host_ip_address_to_bind_to: 0.0.0.0
```

group_vars/monitoring_server.yml
```yaml
monitoring_server: true
install_monitoring_agents: false
host_ip_address_to_bind_to: 0.0.0.0
```

To run the sample playbook:

```sh
cd slingnode-ethereum-examples/slingnode.ethereum_observability/distributed-deployment-layer-per-server
ansible-playbook distributed-deployment-layer-per-server.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```

### distributed-deployment-multiple-layer-per-server:

In this example we deploy each layer on a separate server. As in the previous example the observability components, grafana and elk, are deployed into a dedicates server under the group `monitoring_server`, and agents will be deployed across all the ethereum servers.

Following the previous example the variables are set in the same way.

group_vars/all.yml
```yaml
monitoring_server_host: "{{ hostvars[groups['monitoring_server'][0]]['ansible_facts']['default_ipv4']['address'] }}"
single_server_deployment: false
```

group_vars/ethereum_servers.yml
```yaml
monitoring_server: false
install_monitoring_agents: true
host_ip_address_to_bind_to: 0.0.0.0
```

group_vars/monitoring_server.yml
```yaml
monitoring_server: true
install_monitoring_agents: false
host_ip_address_to_bind_to: 0.0.0.0
```

To run the sample playbook:

```sh
cd slingnode-ethereum-examples/slingnode.ethereum_observability/distributed-deployment-multiple-layer-per-server
ansible-playbook distributed-deployment-multiple-layer-per-server.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```