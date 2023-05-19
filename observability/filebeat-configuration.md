# Filebeat

## Overview

The role uses the official container provided and maintained by Elastic.

* Image source: [https://www.docker.elastic.co/r/beats/filebeat](https://www.docker.elastic.co/r/beats/filebeat)
* Documentation: [https://www.elastic.co/guide/en/beats/filebeat/current/running-on-docker.html](https://www.elastic.co/guide/en/beats/filebeat/current/running-on-docker.html)

## Container

If you want to use different version of container you can modify the following variable:

```yaml
filebeat_docker_image: docker.elastic.co/beats/filebeat:8.7.0
```

### User

The container runs as root.  The reason for this is that it needs permission to read Docker logs located and the socket file under the following paths:

```
/var/lib/docker/containers
/var/run/docker.sock
```

Note: we don't like running container as root and it should be avoided. This will be addressed in future releases.&#x20;

### Volumes

The follwoing volumes are mapped to the container:

```yaml
volumes:
    - {{ observability_root_path }}/filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
    - {{ observability_root_path }}/filebeat/:/registry:rw
    - {{ observability_root_path }}/filebeat/data:/usr/share/filebeat/data:rw
    - /var/run/docker.sock:/var/run/docker.sock:ro
    - /var/lib/docker/containers:/var/lib/docker/containers:ro
```

From top to bottom they are:&#x20;

1. Filebeat config file
2. Registry&#x20;
3. Working dir
4. Socket file required to enable Filebeat's Autodiscovery
5. Docker logs location on the host&#x20;



The role uses Filebeat as log forwarder. Filebeat is configured to autodiscover containers based on container labels and selectively forward logs.&#x20;



