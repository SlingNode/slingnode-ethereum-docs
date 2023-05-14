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

In a single server deployment which is the default type, all components ([monitoring server](architecture/#monitoring-server) and [monitoring agents](architecture/#monitoring-agents)) are deployed to the same server as the Ethereum clients. In this deployment type, the services communicate over Docker Network. It is the same network that the monitored clients are connected to (refer to [Docker network section ](architecture/)in slingnode.ethereum documentation for details).

### Multi tier deployment

In a multi tier, the [monitoring server](architecture/#monitoring-server) is deployed to a dedicated node and configured to monitor Ethereum Clients running on remote machines. In this type of deployment, the monitoring agents (Filebeat, node-exporter, ethereum-metrics-exporter,  cadvisor) are deployed to the servers running the Ethereum clients. The monitoring server and agent are configured to communicate  over the network.&#x20;

## Scalability

Scalability of observability stacks is a big topic. There are a lot of variables  that factor into it. Amongst others:

* Scrape interval
* Logging level
* Retention period
* Number of monitored hosts
* Types of applications&#x20;
* Types of OSes
* Data query patterns (frequency, bucket sizes)
* Availability requirements

SlingNode Observability Stack is meant to be used for monitoring "small deployments". What's small? We have successfully monitored 10 servers running full Ethereum stack (execution, consensus, validator) with scrape interval of 5s, info logging level and 30 days data retention. That's not very scientific, so what can I monitor?&#x20;

| Nodes | Will it work?  |
| ----- | -------------- |
| 1     | definitely     |
| 10    | yes            |
| 20    | probably       |
| 50    | unlikely       |
| 100   | definitely not |

As outlined above this stack will work perfectly fine for a small deployment. However if you need to scale your observability infrastructure and make it "production grade" you will need something more elaborate. SlingNode Team has extensive experience building and maintaining large scale observability solutions, if you think you need one, get in touch at "contact - at - slingnode.com".&#x20;
