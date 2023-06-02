# Exporters

The role deploys the following Prometheus exporters using the official images:

* Node-Exporter&#x20;
* Ethereum-Metrics-Exporter
* Container Advisor

The configuration is defined in templates/dc-observability-agents.yml.j2 file.&#x20;

ethereum-metrics-exporter's port is changed to 9091. The default port collided with Prometheus (9090) in single server deployments. Otherwise default configuration is used.&#x20;

If you want to use different version of containers you can modify the following variables:

```yaml
ethereum_metrics_exporter_docker_image: ethpandaops/ethereum-metrics-exporter:0.21.0
node_exporter_docker_image: quay.io/prometheus/node-exporter:v1.5.0
cadvisor_docker_image: gcr.io/cadvisor/cadvisor:v0.47.1
```
