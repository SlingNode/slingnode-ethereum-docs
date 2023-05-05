# Overview

slingnode.ethereum\_observability is an Ansible role used to deploy a full observability stack that seamlessly integrates with Ethereum nodes deployed using [slingnode.ethereum](https://docs.slingnode.com/slingnode.ethereum/) role. Both roles use the same naming for common variables. This means you can define them once in your Playbook, group\_vars or host\_vars and they will be&#x20;

The stack is comprised of:&#x20;

* ELK
* Filebeat
* Grafana
* Prometheus
* Node-Exporter
* Ethereum-Metrics-Exporter
* Container Advisor

## Deployment type

SlingNode Observability Stack can be deployed to a single server along with Ethereum clients or it can be used in a distributed deployment.&#x20;

### Single server deployment

In a single server deployment which is the default type, all components ([monitoring server](architecture.md#monitoring-server) and [monitoring agents](architecture.md#monitoring-agents)) are deployed to the same server as the Ethereum clients. In this deployment type, the services communicate over Docker Network. It is the same network that the monitored clients are connected to (refer to [Docker network section ](architecture.md)in slingnode.ethereum documentation for details).

### Multi tier deployment

In a multi tier, the [monitoring server](architecture.md#monitoring-server) is deployed to a dedicated node and configured to monitor Ethereum Clients running on remote machines. In this type of deployment, the monitoring agents (Filebeat, node-exporter, ethereum-metrics-exporter,  cadvisor) are deployed to the servers running the Ethereum clients. The monitoring server and agent are configured to communicate  over the network.&#x20;
