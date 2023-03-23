# Logging

The processes running in the containers are configured to log to the console. The logs are saved to the disk using Docker's default [JSON logging driver](https://docs.docker.com/config/containers/logging/configure/).  Logging configuration is defined in each Compose file using YAML anchor syntax as shown below.

```yaml
x-logging: &logging
  logging:
    driver: json-file
    options:
      max-size: 100m
      max-file: "3"
      tag: 'blockchain|execution|geth|{{.ImageName}}|{{.Name}}|{{.ImageFullID}}|{{.FullID}}'
```

The logging is configured to facilitate log collection, shipping and feeding into log analytics solutions such as ELK, SumoLogic, Splunk, etc. In order to simplify this process, the role configures the following:

* JSON format (where available)
* Docker log tags
* Docker labels

## Logging format

The clients are configured to log in JSON format if they support it which makes it easier to feed them into log analytics solutions without additional parsing.&#x20;

### Multiline logs

Some clients generate multiline log entries. Multiline logs combined with Docker logging (which breaks down each line into a separate JSON object) poses a special challenge. Refer to our [blog post](https://slingnode.com/2023/01/10/docker-multiline-logs-and-filebeat-autodiscover/) describing this. The table below summarizes logging features of each client.&#x20;

| Client     | JSON format | Multiline |
| ---------- | :---------: | :-------: |
| Geth       |     yes     |     no    |
| Erigon     |     yes     |     no    |
| Besu       |      no     |    yes    |
| Nethermind |      no     |    yes    |
| Lighthouse |     yes     |     no    |
| Prysm      |     yes     |     no    |
| Teku       |      no     |    yes    |
| Nimbus     |     yes     |     no    |

## Tags

Each Docker compose file contains a tag with details that are useful when managing the logs. The tag contains the following data:

* layer (execution, consensus, validator)
* client name
* Docker image name
* container name
* Full ID of the image&#x20;
* Full ID of the container

### Execution layer tag

Compose template

<pre class="language-yaml"><code class="lang-yaml"><strong>tag: 'blockchain|execution|geth|{{.ImageName}}|{{.Name}}|{{.ImageFullID}}|{{.FullID}}'
</strong></code></pre>

Rendered tag

```
blockchain|execution|nethermind|nethermind/nethermind:latest|execution|sha256:87432e29ccf560fb120ef8cc494a1ea4084f8c48351cf8a5ecab35eb0a2170a4|17839e7900918212e46c2ce7de4bea9434051771206e551970fc5ec191a77ab7
```

### Consensus layer tag&#x20;

Compose template

```yaml
tag: 'blockchain|consensus|lighthouse|{{.ImageName}}|{{.Name}}|{{.ImageFullID}}|{{.FullID}}'
```

Rendered tag

```
blockchain|consensus|teku|consensys/teku:latest|consensus|sha256:843f220f93ca17e97afd51133b3c9ddede47abc72ba1b46ca453695b8ea7d0fd|cbca23d12ee18aaa19f738e351b25872b7f369dd282b9e0cce91eac637a6f42d
```

### Validator layer tag

Compose template

```yaml
tag: 'blockchain|validator|lighthouse|{{.ImageName}}|{{.Name}}|{{.ImageFullID}}|{{.FullID}}'
```

Rendered tag

```
blockchain|validator|teku|consensys/teku:latest|validator|sha256:843f220f93ca17e97afd51133b3c9ddede47abc72ba1b46ca453695b8ea7d0fd|49b8a218bc7f185eabe2c38491f24bd1d3609c1e39c702c7c66f6b730df90df4
```

### Jinja template

The variables prefixed with dot (for example \{{.ImageName\}}) symbol are Docker variables and use the double curly braces syntax for substitution. See [Docker documentation](https://docs.docker.com/config/containers/logging/log\_tags/) for details.  Double curly braces \{{ \}} are treated by Jinja as variables and need to be escaped using \{% raw %\} \{% endraw %\} Jinja tag as shown below.&#x20;

```django
tag: {% raw %}'blockchain|consensus|lighthouse|{{.ImageName}}|{{.Name}}|{{.ImageFullID}}|{{.FullID}}'{% endraw %}
```

## Docker labels

The containers have custom [labels](https://docs.docker.com/compose/compose-file/compose-file-v3/#labels-4) defined in the Compose files. There two tags that specify the "layer" and the "client". The labels are required to allow for selective log collection using Filebeat. Refer to our [blog post](https://slingnode.com/2023/01/10/docker-multiline-logs-and-filebeat-autodiscover/) for details.&#x20;

```yaml
    labels:
      slingnode_client: "lighthouse"
      slingnode_layer: "consensus"
```

## slingnode.ethereum\_observability

SlingNode has developed a[n Ansible role that can deploy an Observability stack](https://slingnode.gitbook.io/slingnode.ethereum\_observability/) that seamlessly integrates with nodes deployed using slingnode.ethereum role. The role enables log parsing and forwarding to ElasticSearch.&#x20;
