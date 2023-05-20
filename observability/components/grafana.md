# Grafana

## Overview

Grafana is deployed using the official image:

* Image source: [https://hub.docker.com/r/grafana/grafana](https://hub.docker.com/r/grafana/grafana)

## Container

If you want to use different version of container you can modify the following variable:

```yaml
grafana_docker_image: grafana/grafana:9.5.1
```

#### Data persistence

Prometheus container uses named volume to persist the data. The container can be safely deleted and recreated without the loss of data.

```yaml
    volumes:
      - grafana-data:/var/lib/grafana
```

**Deleting data**

To delete the data and start from scratch you will need to execute the following command on the server:

```
docker kill grafana 
docker container rm grafana 
docker volume rm observability_dc_grafana-data
```

## Configuration

The role deploys a default instance of Grafana and does not change any settings.&#x20;

### Provisioning

The [provisioning feature](https://grafana.com/docs/grafana/latest/administration/provisioning/) is used to create data source for Prometheus and specify directory from which Grafana will load the dashboards if any are present.&#x20;

The Prometheus data source name is "Prometheus"

```yaml
deleteDatasources:
  - name: Prometheus
    orgId: 1
```

The dashboard directory is shown below.&#x20;

```yaml
    options:
      path: /etc/grafana/provisioning/dashboards
```

### Dashboards

The easiest way to add dashboards is to import them from Grafana Gallery. You will have option to edit source name during the import. If you want to deploy dashboards as files you will need to copy them to the directory specified above. &#x20;
