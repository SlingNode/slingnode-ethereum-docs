# Metrics

By default Prometheus metrics are enabled for all clients. The metrics are exposed to the Docker network and accessible only from other containers - refer to [Exposing ports](architecture/exposing-ports.md) page for details on exposing them to the host or to the network.&#x20;

The metrics can be disabled by overriding the following variables and setting them to false:

```yaml
execution_client_metrics_enabled: true
consensus_client_metrics_enabled: true
validator_client_metrics_enabled: true
```

The ports the metrics are exposed on can be modified using the following variables:

```yaml
execution_client_metrics_port: 6060
consensus_client_metrics_port: 8008
validator_client_metrics_port: 8009
```

### Geth expensive metrics

Be default Geth's expensive metrics are enabled. They can be disabled by overriding the following variable and setting it to false:&#x20;

```yaml
geth_metrics_expensive_enabled: true
```

## slingnode.ethereum\_observability

SlingNode has developed an [Ansible role that can deploy an Observability stack](https://slingnode.gitbook.io/slingnode.ethereum\_observability/) that seamlessly integrates with nodes deployed using slingnode.ethereum role. The role deploys Prometheus that will automatically start scraping the metrics.&#x20;
