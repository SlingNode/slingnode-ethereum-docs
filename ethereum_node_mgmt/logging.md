# Logging

The container logging is configured to seamlessly integrate with observability stack deployed using slingnode.ethereum\_observability role.&#x20;

Refer to [slingnode.ethereum documentation ](http://localhost:5000/s/Ib9tX0kORM9rMi1DWQvF/logging)for details regarding logging.

Loggin is crucial for the tasks executed using the ephemeral containers. All containers use the following tags.&#x20;

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
