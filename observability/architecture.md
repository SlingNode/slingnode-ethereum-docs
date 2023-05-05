# Architecture

## Monitoring server



## Monitoring agents



### Security

In multi tier deployments the monitoring traffic sent over the network is unencrypted.&#x20;

### Docker compose



### Docker network

Docker compose services (elk, grafana, prometheus, filebeat, exporters) are "connected" to the same Docker network. The network is defined in each both Compose files.

```yaml
networks:
  service_net:
    name: service_net
```
