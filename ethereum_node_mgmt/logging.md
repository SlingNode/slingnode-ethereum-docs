# Logging

At a high level, a temporary container will be created for each task, execute the specified command and exit. The downside of this approach is that there is no convenient way to know what happened during the execution of the command (the container is ephemeral therefore it is difficult to inspect the logs).  The containers are configured to log using the Docker's default json-file driver. The logs will be generated and saved to the container log. They will be available only during the container lifetime. Therefore it is recommended to use a log aggregation solution (ELK, Loki, SumoLogic, etc) in order to inspect the logs.&#x20;

## Log tags

The containers use the following tags.  You can filter logs in your log management solution for "validator\_management" to view the output of the executed commands. Refer to [Client specific behavior](client-specific-behavior/) section of this documentation for reference of expected logs for each client task. Logging uses the same configuration as [slingnode.ethereum ](http://localhost:5000/s/Ib9tX0kORM9rMi1DWQvF/logging)role.

```yaml
version: "3.9"
x-logging: &logging
  logging:
    driver: json-file
    options:
      max-size: 100m
      max-file: "3"
      tag: 'blockchain|validator_management|lighthouse|{{.ImageName}}|{{.Name}}|{{.ImageFullID}}|{{.FullID}}'
```

## Observability

The logging is configured to integrate with Observability stack deployed using [slingnode.ethereum\_observability](https://docs.slingnode.com/slingnode.ethereum\_observability) role. You can use the observability role tp deploy a full monitoring stack for you Ethereum node.
