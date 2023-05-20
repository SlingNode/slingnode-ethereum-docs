# Overview

slingnode.ethereum\_observability is an Ansible role used to deploy a full observability stack that seamlessly integrates with Ethereum nodes deployed by [slingnode.ethereum](https://docs.slingnode.com/slingnode.ethereum/) role. Both roles use the same naming for common variables. This means you can define them once in your Playbook, group\_vars or host\_vars and deploy a full Ethereum node along with the observability stack.&#x20;

Out of the box you get a fully functional stack with:

* ethereum client metrics
* node metrics
* container metrics
* parsed and aggregated client logs&#x20;
* dashboarding solution

The stack is comprised of:&#x20;

* ELK
* Filebeat
* Grafana
* Prometheus
* Node-Exporter
* Ethereum-Metrics-Exporter
* Container Advisor

## Deployment types

SlingNode Observability Stack (SOS) can be deployed on a single server along with Ethereum clients or it can be used in a distributed deployment.&#x20;

### Single server deployment

In a single server deployment which is the default type, all components ([monitoring server](architecture/#monitoring-server) and [monitoring agents](architecture/#monitoring-agents)) are deployed to the same server as the Ethereum clients. In this deployment type, the services communicate over Docker Network. It is the same network that the monitored clients are connected to (refer to [Docker network section ](architecture/)in slingnode.ethereum documentation for details).&#x20;

<figure><img src=".gitbook/assets/slingnode-ethereum-observabiliyt-single.png" alt=""><figcaption><p>Single server deployment</p></figcaption></figure>

### Distributed deployment

In a distributed, the [monitoring server](architecture/#monitoring-server) is deployed to a dedicated node and configured to monitor Ethereum Clients running on remote servers. In this type of deployment, the monitoring agents (Filebeat, node-exporter, ethereum-metrics-exporter,  cadvisor) are deployed to the servers running the Ethereum clients. The monitoring server and agent are configured to communicate  over the network.&#x20;

<figure><img src=".gitbook/assets/slingnode-ethereum-observabiliyt-distributed.png" alt=""><figcaption><p>Distributed deployment</p></figcaption></figure>

## Scalability

Scalability of an observability stacks is a big topic. There are a lot of variables  that factor into it. Amongst others:

* Scrape interval
* Logging level
* Retention period
* Number of monitored hosts
* Types of applications&#x20;
* Types of OSes
* Data query patterns (frequency, bucket sizes)
* Availability requirements

SlingNode Observability Stack is meant to be used for monitoring "small deployments". What's small? We have successfully monitored 10 servers running full Ethereum stack (execution, consensus, validator) with scrape interval of 5s, info logging level and 30 days data retention.&#x20;

We don't have any precise benchmarks.&#x20;

As outlined above this stack will work perfectly fine for a "small deployment". However if you need to scale your observability infrastructure and make it "production grade" you will need something more elaborate. SlingNode Team has extensive experience building and maintaining large scale observability solutions and is ready to help out. Contat us at  "contact - at - slingnode.com".&#x20;
