# Role variables

The variable naming and layout is the same as in the [slingnode.ethereum](https://docs.slingnode.com/slingnode.ethereum/role-variables) role. This is a deliberate design choice which enables seamless integration of both roles and full node deployment in a single Playbook.

Role variables are defined in 'defaults'. This means they have the lowest precedence and can be easily overridden. See [Ansible documentation](https://docs.ansible.com/ansible/latest/playbook\_guide/playbooks\_variables.html#understanding-variable-precedence) for details on the precedence.

| Variable location         | Purpose                                                          |
| ------------------------- | ---------------------------------------------------------------- |
| defaults/main/main.yml    | generic variables for the role; common variables for all clients |
| vars/\{{os\_family\}}.yml | variables specific to the OS                                     |

### Important variables <a href="#important-variables" id="important-variables"></a>

This section outlines variables that you will most likely want to modify.

Role behavior is dictated by the following variables. They allow you to specify what components of the stack get deployed. Refer to the [Architecture](architecture/) section for details.&#x20;

```yaml
monitoring_server: true            # deploys ELK, Grafana, Prometheus
install_monitoring_agents: true    # deploys Filebeat, CAdvisor, node-exporter, ethereum-metrics-exporter
single_server_deployment: true     # specifies if the stack is being deployed on  the same server as the Ethereum clients
```

The defaults will work for a single server deployment. If you want to install monitoring server on a dedicated host and monitor endpoints (Ethereum clients) running on remote hosts.
