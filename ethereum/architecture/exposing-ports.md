# Exposing ports

The role defaults to a "single server" deployment and only exposes client p2p ports to the host. All other ports are accessible only over the [Docker network](./#docker-network). This means that (when enabled) execution layer JSON RPC API, consensus layer beacon API and metrics can be accessed only from other containers connected to the same [Docker network](./#docker-network). This default configuration is considered a "safe default" and will work with observability services (such as Prometheus) running on the same server. However, you will not be able to access them from the Docker host or other hosts.&#x20;

In most cases you will want to customize this behavior to suite your needs. This page describes the main variables that can be used to customize the configuration.&#x20;

The default port mapping looks as follows when viewed using docker ps (the exact command is: docker ps --format '\{{ .Names \}}\t\{{ .Ports \}}'

```
NAMES           PORTS
execution	6060/tcp, 8545-8546/tcp, 8551/tcp, 0.0.0.0:30303->30303/tcp, 0.0.0.0:30303->30303/udp
consensus	5052/tcp, 8008/tcp, 0.0.0.0:9000->9000/tcp, 0.0.0.0:9000->9000/udp
validator	8009/tcp
```

## Exposing ports to Docker host

As explained before, by default the container ports (with exception of the p2p ports)  are accessible only over the Docker network. Each port (service) has a corresponding variable that when set to "true" will expose it to the host by mapping it to the loop-back interface (127.0.0.1) of the host. This will make the ports accessible from the host running Docker engine but not from remote hosts. To access the exposed services you'll need to use either a reverse proxy (such as Nginx) or setup [SSH tunnels](https://www.ssh.com/academy/ssh/tunneling-example).&#x20;

To expose the following ports to the host:

* execution client's authenticated JSON RPC&#x20;
* consensus client's beacon API
* metrics for all clients&#x20;

&#x20;we would set the following variables to true:

```yaml
expose_execution_client_authrpc_port_to_host: true
expose_consensus_client_http_port_to_host: true
expose_execution_client_metrics_port_to_host: true
expose_consensus_client_metrics_port_to_host: true
expose_validator_client_metrics_port_to_host: true
```

This is what it looks like viewed using docker ps:&#x20;

```
NAMES           PORTS
execution	127.0.0.1:6060->6060/tcp, 127.0.0.1:8551->8551/tcp, 8545-8546/tcp, 0.0.0.0:30303->30303/tcp, 0.0.0.0:30303->30303/udp
consensus	127.0.0.1:5052->5052/tcp, 127.0.0.1:8008->8008/tcp, 0.0.0.0:9000->9000/tcp, 0.0.0.0:9000->9000/udp
validator	127.0.0.1:8009->8009/tcp
```

Refer to[ Enabling Validator client API](../enabling-validator-client-api.md) for another example.&#x20;

## Exposing ports to remote hosts

<mark style="color:red;">**Configuration outlined in this section is suitable only if you run your servers on a private network behind a network firewall (or security groups), otherwise sensitive APIs and metrics will be exposed to the Internet even if your server has  FirewallD or UFW enabled due to how Docker modifies IPTables. Refer to**</mark> [<mark style="color:red;">**Docker & host firewall**</mark>](docker-and-host-firewall.md) <mark style="color:red;">**section for details.**</mark>&#x20;

To expose ports to the host and make them accessible from remote hosts you can override the following variable:&#x20;

```yaml
host_ip_address_to_bind_to: 0.0.0.0
```

This is what it looks like viewed using docker ps:

```
NAMES           PORTS
execution	0.0.0.0:6060->6060/tcp, 0.0.0.0:8551->8551/tcp, 0.0.0.0:30303->30303/tcp, 8545-8546/tcp, 0.0.0.0:30303->30303/udp
consensus	0.0.0.0:5052->5052/tcp, 0.0.0.0:8008->8008/tcp, 0.0.0.0:9000->9000/tcp, 0.0.0.0:9000->9000/udp
validator	0.0.0.0:8009->8009/tcp
```

This configuration makes the services accessible over the network.

## Available variables

The variables that can used to define how ports are exposed use the following naming convention:

```
expose_{{ layer }}_{{ API }}_port_to_host
```

```yaml
defaults/main/main.yml

expose_execution_client_json_rpc_port_to_host: false
expose_execution_client_authrpc_port_to_host: false
expose_execution_client_json_rpc_websocket_port_to_host: false
expose_execution_client_metrics_port_to_host: false
expose_consensus_client_http_port_to_host: false
expose_consensus_client_metrics_port_to_host: false
expose_validator_client_metrics_port_to_host: false
expose_validator_api_port_to_host: false

defaults/main/prysm.yml

expose_prysm_rpc_port_to_host: false
```
