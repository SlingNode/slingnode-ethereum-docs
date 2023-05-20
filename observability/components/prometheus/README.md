# Prometheus

## Overview

Prometheus is deployed using the official image:

* Image source: [https://hub.docker.com/r/prom/prometheus](https://hub.docker.com/r/prom/prometheus)

## Container

If you want to use different version of container you can modify the following variable:

```yaml
prometheus_docker_image: prom/prometheus:v2.44.0
```

### Data persistence

Prometheus container uses named volume to persist the data. The container can be safely deleted and recreated without the loss of data.

```yaml
    volumes:
       - prom-data:/prometheus
```

#### Deleting data

To delete the data and start from scratch you will need to execute the following command on the server:

```bash
docker kill prometheus 
docker container rm prometheus 
docker volume rm observability_dc_prom-data
```

## Configuration

How Prometheus is configured is dictated by the deployment type. In a single server deployment, Prometheus uses a "static" configuration defined in prometheus.yml file. In a distributed deployment,  [file based Service Discovery](https://prometheus.io/docs/guides/file-sd/) feature of Prometheus is used. In both cases the configuration is fully parameterized and customizable. You can review the configuration templates here: [https://github.com/SlingNode/slingnode-ansible-ethereum-observability/tree/master/templates/prometheus](https://github.com/SlingNode/slingnode-ansible-ethereum-observability/tree/master/templates/prometheus)

### Single server deployment

In a single server deployment, Prometheus uses a "static" configuration defined in prometheus.yml file. The config file always contains scrape targets for all agents (node-exporter, cAdvisor, ethereum-metrics-exporter). The targets for Ethereum clients are generated dynamically based on the types of clients that are deployed.&#x20;

You can customize Prometheus by providing your own configuration file.&#x20;

```yaml
# Use the below variables to provide your own Prometheus config file:
prometheus_config_template: templates/prometheus/prometheus.yml.j2
```

### Distributed deployment

In a distributed deployment,  [file based Service Discovery](https://prometheus.io/docs/guides/file-sd/) feature of Prometheus is used. In summary the file based Service Discover (SD) allows for listing scrape target hosts in text files. Prometheus periodically reads those files and starts scraping new hosts. There are two SD file templates included in the role:&#x20;

* sd\_all\_targets.yml - includes all agent targets (node-exporter, cAdvisor, ethereum-metrics-exporter), all hosts run the agents&#x20;
* sd\_client\_targets.yml - includes Ethereum client targets with config depenend on the type of the client

Both files will be automatically populated based on the group\_vars or host\_vars. &#x20;

You can  customize the configuration by providing your own configuration files.

```yaml
# If you customized Prometheus config file and don't use Service Dicovery
# set this variable to an empty list in your Playbook
prometheus_service_discovery_target_templates:
  - src: templates/prometheus/{{ prometheus_service_discovery_all_targets_file }}.j2
    dest: "{{ observability_root_path }}/prometheus/{{ prometheus_service_discovery_all_targets_file }}"
  - src: templates/prometheus/{{ prometheus_service_discovery_client_targets_file }}.j2
    dest: "{{ observability_root_path }}/prometheus/{{ prometheus_service_discovery_client_targets_file }}"

prometheus_service_discovery_all_targets_file: sd_all_targets.yml
prometheus_service_discovery_client_targets_file: sd_client_targets.yml
```
