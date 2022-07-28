---
title: Reference - Self-hosted gateway settings - Azure API Management
description: Reference for the required and optional settings to configure the Azure API Management self-hosted gateway.
services: api-management
author: dlepow

ms.service: api-management
ms.topic: reference
ms.date: 06/28/2022
ms.author: danlep
---

# Reference: Self-hosted gateway configuration settings

This article provides a reference for required and optional settings that are used to configure the API Management [self-hosted gateway](self-hosted-gateway-overview.md). 

> [!IMPORTANT]
> This reference applies only to the self-hosted gateway v2.

## Deployment

| Name                           | Description              | Required | Default           |
|----|------|----------|-------------------|
| config.service.endpoint | Configuration endpoint in Azure API Management for the self-hosted gateway. Find this value in the Azure portal under **Gateways** > **Deployment**.  | Yes       | N/A             |
| config.service.auth | Access token (authentication key) of the self-hosted gateway. Find this value in the Azure portal under **Gateways** > **Deployment**. | Yes | N/A |


##  Metrics

| Name                           | Description              | Required | Default           |
|----|------|----------|-------------------|
| telemetry.metrics.local | Enable [local metrics collection](how-to-configure-local-metrics-logs.md) through StatsD. Value is one of the following: `none`, `statsd`. | No | `none` |
| telemetry.metrics.local.statsd.endpoint | StatsD endpoint. | Yes, if `telemetry.metrics.local` is set to `statsd`; otherwise no.  | N/A |
| telemetry.metrics.local.statsd.sampling | StatsD metrics sampling rate. Value must be between 0 and 1, for example, 0.5. |  No | N/A |
| telemetry.metrics.local.statsd.tag-format | StatsD exporter [tagging format](https://github.com/prometheus/statsd_exporter#tagging-extensions). Value is one of the following: `ibrato`, `dogStatsD`, `influxDB`. | No | N/A |
| telemetry.metrics.cloud | Indication whether or not to [enable emitting metrics to Azure Monitor](how-to-configure-cloud-metrics-logs.md). | No |    `true` |
| observability.opentelemetry.enabled | Indication whether or not to enable [emitting metrics to an OpenTelemetry collector](how-to-deploy-self-hosted-gateway-kubernetes-opentelemetry.md) on Kubernetes. | No | `false` |
| observability.opentelemetry.collector.uri | URI of the OpenTelemetry collector to send metrics to. | Yes, if `observability.opentelemetry.enabled` is set to `true`; otherwise no. | N/A |
| observability.opentelemetry.histogram.buckets | Histogram buckets in which OpenTelemetry metrics should be reported. Format: "*x,y,z*,...". | No | "5,10,25,50,100,250,500,1000,2500,5000,10000" |

## Logs

| Name   | Description | Required | Default |
| ------------- | ------------- | ------------- | ----|
| telemetry.logs.std  |[Enable  logging](how-to-configure-local-metrics-logs.md#logs) to a standard stream. Value is one of the following: `none`, `text`, `json`. | No |  `text` | 
| telemetry.logs.local  | [Enable local logging](how-to-configure-local-metrics-logs.md#logs). Value is one of the following: `none`, `auto`, `localsyslog`, `rfc5424`, `journal`, `json`  | No  | `auto` |
| telemetry.logs.local.localsyslog.endpoint  |  localsyslog endpoint.  | Yes if `telemetry.logs.local` is set to `localsyslog`; otherwise no. | N/A |
| telemetry.logs.local.localsyslog.facility  | Specifies localsyslog [facility code](https://en.wikipedia.org/wiki/Syslog#Facility), for example, `7`. | No | N/A |
| telemetry.logs.local.rfc5424.endpoint  |  rfc5424 endpoint.  | Yes if `telemetry.logs.local` is set to `rfc5424`; otherwise no. | N/A |
| telemetry.logs.local.rfc5424.facility  | Facility code per [rfc5424](https://tools.ietf.org/html/rfc5424), for example, `7`  | No | N/A |
| telemetry.logs.local.journal.endpoint  | Journal endpoint.   |Yes if `telemetry.logs.local` is set to `journal`; otherwise no. | N/A |
| telemetry.logs.local.json.endpoint | UDP endpoint that accepts JSON data, specified as file path, IP:port, or hostname:port. | Yes if `telemetry.logs.local` is set to `json`; otherwise no. | 127.0.0.1:8888  |

## Security

| Name  | Description | Required | Default |
| ------------- | ------------- | ------------- | ----|
| certificates.local.ca.enabled | Indication whether or not to the self-hosted gateway should use local CA certificates that are mounted. This requires the self-hosted gateway to run as root or with user ID 1001. | No | `false` |
| net.server.tls.ciphers.allowed-suites |   Comma-separated list of ciphers to use for TLS connection between API client and the self-hosted gateway. | No | N/A |
| net.client.tls.ciphers.allowed-suites | Comma-separated list of ciphers to use for TLS connection between the self-hosted gateway and the backend. | No | N/A |

## How to configure settings

### Kubernetes YAML file

When deploying the self-hosted gateway to Kubernetes using a [YAML file](how-to-deploy-self-hosted-gateway-kubernetes.md), configure settings as name-value pairs in the `data` element of the gateway's ConfigMap. For example:

```yml
apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "contoso.configuration.azure-api.net"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"

[...]

```

### Helm chart

When using [Helm](how-to-deploy-self-hosted-gateway-kubernetes-helm.md) to deploy the self-hosted gateway to Kubernetes, pass [chart configuration settings](https://artifacthub.io/packages/helm/azure-api-management/azure-api-management-gateway) as parameters to the `helm install` command. For example:

```
helm install azure-api-management-gateway \
    --set gateway.configuration.uri='contoso.configuration.azure-api.net' \
    --set gateway.auth.key='GatewayKey contosogw&xxxxxxxxxxxxxx...' \
    --set secret.createSecret=false \
    --set secret.existingSecretName=`mysecret` \
    azure-apim-gateway/azure-api-management-gateway
```


## Next steps

-   Learn more about guidance for [running the self-hosted gateway on Kubernetes in production](how-to-self-hosted-gateway-on-kubernetes-in-production.md)
-   [Deploy self-hosted gateway to Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Deploy self-hosted gateway to Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
-   [Deploy self-hosted gateway to Azure Arc-enabled Kubernetes cluster](how-to-deploy-self-hosted-gateway-azure-arc.md)




