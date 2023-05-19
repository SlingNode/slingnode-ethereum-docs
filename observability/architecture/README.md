# Architecture

## Overview

Conceptually the stack is broken down to two components:

* monitoring server
* monitoring agents

Monitoring server comprises of:

* ELK
* Grafana
* Prometheus

Monitoring agents comprise of:

* Filebeat
* CAdvisor
* node-exporter
* ethereum-metrics-exporter

By default both monitoring server and monitoring agents are installed and configured to monitor Ethereum clients running on the same server (single\_server\_deployment).&#x20;

## Docker compose

The role utilizes Docker Compose. Each component (monitoring server and monitoring agents) is defined in a separate Compose file. The compose files are templated using Jinja2 and are dynamically generated when the role runs.&#x20;

```
docker-compose
├── dc-observability-agents.yml.j2     # monitoring agents
└── dc-observability.yml.j2            # monitoring servers
```

### Docker network

Docker compose services (elk, grafana, prometheus, filebeat, exporters) are "connected" to the same Docker network. The network is defined in both Compose files.&#x20;

```yaml
networks:
  service_net:
    name: service_net
```

Ethereum clients deployed using slingnode.ethereum role are connected to the same Docker network. This means they can freely communicate without the need to expose any ports.&#x20;

### Data persistence

ELK, Grafana and Prometheus containers use named volumes in order to persist the data. The following volumes are defined in the compose file:

```yaml
volumes:
  elk-data:
  grafana-data:
  prom-data:
```

Filebeat is the only "monitoring agent" container that persists data. The following volume is defined in the compose file:

```yaml
volumes:
  filebeat-data:
```

The remaining containers do not persist any data.&#x20;

### Starting the Docker compose project

The compose templates are rendered and copied to the target server location defined by the following variables:&#x20;

```yaml
observability_root_path: /opt/observability
observability_docker_compose_path: "{{ observability_root_path }}/observability_dc"
```

## Directory structure

The role creates the following directory structure.

```
opt/
└── elk
    ├── filebeat
    ├── grafana
    │   └── provisioning
    │       ├── dashboards
    │       └── datasources
    ├── observability_dc
    │   └── agents
    └── prometheus
```

The location can be modified by overriding the following variable:

```yaml
observability_root_path: /opt/observability
```

## Connectivity between monitoring server and endpoints

As outlined in the [Docker network ](./#docker-network)section, in single server deployments all network communication happens over Docker network. By its nature, in a distributed deployment where monitoring server is installed in a separate server than the endpoints it monitors the communication occurs over the network.&#x20;

A high level traffic flow looks as follows:&#x20;

* Prometheus (monitoring server) -> endpoints (scrape targets)
* Filebeat (endpoint/monitoring agent) -> ELK (monitoring server)

### Exposing ports

The role defaults to a "single server" deployment and maps container ports to the Docker host's localhost interface. This means that you will need a reverse proxy (with TLS) or SSH tunnels to access the web interfaces (Kibana, Grafana, Prometheus, CAdvisor).&#x20;

The default port mapping looks as follows when viewed using docker ps (the exact command is: docker ps --format '\{{ .Names \}}\t\{{ .Ports \}}'

```
NAMES                       PORTS
prometheus	            127.0.0.1:9090->9090/tcp
elk	                    127.0.0.1:5044->5044/tcp, 127.0.0.1:5601->5601/tcp, 127.0.0.1:9200->9200/tcp, 9300/tcp
grafana	                    127.0.0.1:3000->3000/tcp
cadvisor	            127.0.0.1:8080->8080/tcp
node-exporter	            127.0.0.1:9100->9100/tcp
ethereum-metrics-exporter   127.0.0.1:9091->9091/tcp
filebeat
```

Note: metrics exporters do not need to be mapped to the localhost since in a single server deployment Prometheus scrapes them over Docker network. They are accessible on the localhost to make automated role testing easier.&#x20;

### Exposing ports to remote hosts

<mark style="color:red;">**Configuration outlined in this section is suitable only if you run your servers on a private network behind a network firewall (or security groups), otherwise sensitive APIs and metrics will be exposed to the Internet even if your server has FirewallD or UFW enabled due to how Docker modifies IPTables. Refer to**</mark> [<mark style="color:blue;">**Docker & host firewall**</mark> ](https://docs.slingnode.com/slingnode.ethereum/architecture/docker-and-host-firewall)<mark style="color:red;">**section for details.**</mark>

To expose ports to the host and make them accessible from remote hosts you can override the following variable:

```yaml
host_ip_address_to_bind_to: 0.0.0.0
```

Mapping ports to 0.0.0.0 is required for distributed deployments when Monitoring server communicates with the endpoints (Prometheus scraping) over the network to scrape node-exporter and ethereum-metrics-exporter metrics.&#x20;

This is what it looks like viewed using docker ps on a monitored endpoint:

```
NAMES                       PORTS
cadvisor	            0.0.0.0->8080/tcp
node-exporter	            0.0.0.0:9100->9100/tcp
ethereum-metrics-exporter   0.0.0.0:9091->9091/tcp
filebeat
```

This is also required on the monitoring server in order to expose Logstash port for log forwarding by Filebeat.
