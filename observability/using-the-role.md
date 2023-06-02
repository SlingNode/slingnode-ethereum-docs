# Using the role

The best place to start is to check the examples project. There are example playbooks that cover different deployment types.

### Accessing the web UIs

Grafana and Kibana web UIs are by default mapped to the localhost interface of the Docker host. It expected that role users will deploy a reverse proxy (such as NGINX) with TLS or SSH tunnels in order access the web UIs.

### Viewing logs in Kibana

In order to view the logs you will need to create a data source first. Follow [this steps ](components/elk/creating-kibana-data-view.md)this stepsto create one.

## Getting started

To get started clone the examples repository and install the requirements:

```sh
git clone git@github.com:SlingNode/slingnode-ethereum-examples.git
cd slingnode-ethereum-examples/slingnode.ethereum_observability
ansible-galaxy install -r requirements.yml
```

## Single server deployments

In this scenario the observability stack components run on the same server as the monitored Ethereum clients. The monitoring server, monitoring agents and the clients communicate over internal Docker network.

A high-level end state is:

* Grafana, ELK, Prometheus exposed to the host
* Filebeat, node-exporter, ethereum-metrics-exporter, cadvisor exposed to the internal docker network
* All network communication happens over Docker network

#### single-node-deployment:

This example will deploy all the observability components on a single server where Ethereum clients are running

```sh
cd slingnode-ethereum-examples/slingnode.ethereum_observability/single-node-deployment
ansible-playbook single-node-deployment.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```

We don't need to specify any variables specific to the observability role for this example to work.

## Distributed deployments

In the context of the slingnode.observability role a distributed deployment is where monitoring server (Prometheus, ELK, Grafana) run on a different server than the one it monitors. The monitored Ethereum nodes can run whole stack on each server or can themselves be distributed running different Ethereum layers on different servers.&#x20;

This section outlines relevant example Playbooks, inventories and group\_vars.&#x20;

In these scenarios the components communicate over the LAN, the agents ports are exposed to the host and accessible from anywhere. <mark style="color:red;">This kind of deployment is suitable only when your hosts are protected by a network firewall or security groups</mark>.

Note: there are multiple ways of achieving similar results, the implementation depends on the goal you want to achieve. This is an example only and it should be adapted to your needs.

A high-level end state is:

* Grafana, ELK, Prometheus exposed to the host running on a dedicated monitoring server
* Filebeat, node-exporter, ethereum-metrics-exporter, cadvisor deployed to the servers running Ethereum clients and accssible from remote hosts (ports mapped to 0.0.0.0).
* Network communication between monitoring server and the agents/clients happens over local network

### distributed-deployment-multiple-layer-per-server

In this example each Ethereum client layer is deployed to a separate server. In this scenario one type of the client to the first 25 servers in each host group and a different client to the remaining 25 servers in each host group.

The monitoring server is deployed to a dedicates server using `monitoring_server`host group and agents are deployed to all  ethereum servers.

In this case we need to define the following variables:

#### group\_vars/all.yml

```yaml
monitoring_server_host: "{{ hostvars[groups['monitoring_server'][0]]['ansible_facts']['default_ipv4']['address'] }}"
single_server_deployment: false
```

The above variables define the monitoring server IP address that is required for Filebeat to know where to send the logs and specify that this is a distributed deployment (single\_server\_deployment: false)

#### group\_vars/ethereum\_servers.yml

```yaml
monitoring_server: false
install_monitoring_agents: true
host_ip_address_to_bind_to: 0.0.0.0
```

The above variables apply to all servers running Ethereum clients and specify:

* not to install monitoring server
* install monitoring agents
* map container ports to 0.0.0.0 (make the the accessible from remote hosts)&#x20;

#### group\_vars/monitoring\_server.yml

```yaml
monitoring_server: true
install_monitoring_agents: false
host_ip_address_to_bind_to: 0.0.0.0
```

The above variables apply to the monitoring server and specify:

* to install monitoring server
* not to install monitoring agents
* map container ports to 0.0.0.0 (make the the accessible from remote hosts)&#x20;

#### To run the sample playbook:

```sh
cd slingnode-ethereum-examples/slingnode.ethereum_observability/distributed-deployment-multiple-layer-per-server
ansible-playbook distributed-deployment-multiple-layer-per-server.yml -i inventory.ini --private-key ~/.ssh/ubuntu.privatekey  --user ubuntu
```
