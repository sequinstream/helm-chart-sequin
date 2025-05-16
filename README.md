<!--- app-name: Sequin -->

# Sequin Helm Chart

Sequin is a powerful data streaming and processing platform that integrates with PostgreSQL and Redis for robust data management capabilities.

[Overview of Sequin](https://github.com/sequinstream/sequin)

Trademarks: This software listing is packaged following Bitnami standards. The respective trademarks mentioned in the offering are owned by the respective companies, and use of them does not imply any affiliation or endorsement.

## TL;DR

```console
helm install my-release oci://registry-1.docker.io/sequinstream/sequin
```

## Introduction

This chart bootstraps a [Sequin](https://github.com/sequinstream/sequin) deployment on a [Kubernetes](https://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- PV provisioner support in the underlying infrastructure

## Installing the Chart

To install the chart with the release name `my-release`:

```console
helm install my-release oci://REGISTRY_NAME/REPOSITORY_NAME/sequin
```

> Note: You need to substitute the placeholders `REGISTRY_NAME` and `REPOSITORY_NAME` with a reference to your Helm chart registry and repository.

The command deploys Sequin on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

### Global parameters

| Name                      | Description                                     | Value |
| ------------------------- | ----------------------------------------------- | ----- |
| `global.imageRegistry`    | Global Docker image registry                    | `""` |
| `global.imagePullSecrets` | Global Docker registry secret names as an array | `[]`  |

### Common parameters

| Name                | Description                                        | Value           |
| ------------------- | -------------------------------------------------- | --------------- |
| `kubeVersion`       | Override Kubernetes version                        | `""`            |
| `nameOverride`      | String to partially override common.names.fullname | `""`            |
| `fullnameOverride`  | String to fully override common.names.fullname     | `""`            |
| `commonLabels`      | Labels to add to all deployed objects              | `{}`            |
| `commonAnnotations` | Annotations to add to all deployed objects         | `{}`            |
| `clusterDomain`     | Kubernetes cluster domain name                     | `cluster.local` |

### Sequin Image parameters

| Name                | Description                                          | Value            |
| ------------------- | ---------------------------------------------------- | ---------------- |
| `image.registry`    | Sequin image registry                                | `docker.io`      |
| `image.repository`  | Sequin image repository                              | `sequin/sequin`  |
| `image.tag`         | Sequin image tag (immutable tags are recommended)    | `latest`         |
| `image.digest`      | Sequin image digest in the way sha256:aa....         | `""`             |
| `image.pullPolicy`  | Sequin image pull policy                             | `IfNotPresent`   |
| `image.pullSecrets` | Sequin image pull secrets                            | `[]`             |
| `image.debug`       | Enable Sequin image debug mode                       | `false`          |

### Sequin Configuration parameters

| Name                    | Description                                      | Value       |
| ----------------------- | ------------------------------------------------ | ----------- |
| `postgresqlPoolSize`    | The maximum number of concurrent connections to PostgreSQL | `10`        |
| `redisPoolSize`         | Number of Redis connections in the pool          | `5`         |
| `existingSecret`        | Name of a secret with the application password   | `""`        |
| `command`               | Override default container command               | `[]`        |
| `args`                  | Override default container args                  | `[]`        |
| `extraEnvVars`          | Array with extra environment variables           | `[]`        |
| `extraEnvVarsCM`        | Name of existing ConfigMap containing extra env vars | `""`        |
| `extraEnvVarsSecret`    | Name of existing Secret containing extra env vars | `""`        |

### Sequin deployment parameters

| Name                                    | Description                                                      | Value           |
| --------------------------------------- | ---------------------------------------------------------------- | --------------- |
| `livenessProbe.enabled`                 | Enable livenessProbe on Sequin containers                        | `true`          |
| `livenessProbe.initialDelaySeconds`     | Initial delay seconds for livenessProbe                          | `5`             |
| `livenessProbe.periodSeconds`           | Period seconds for livenessProbe                                 | `10`            |
| `livenessProbe.timeoutSeconds`          | Timeout seconds for livenessProbe                                | `5`             |
| `livenessProbe.failureThreshold`        | Failure threshold for livenessProbe                              | `5`             |
| `livenessProbe.successThreshold`        | Success threshold for livenessProbe                              | `1`             |
| `readinessProbe.enabled`                | Enable readinessProbe on Sequin containers                       | `true`          |
| `readinessProbe.initialDelaySeconds`    | Initial delay seconds for readinessProbe                         | `5`             |
| `readinessProbe.periodSeconds`          | Period seconds for readinessProbe                                | `10`            |
| `readinessProbe.timeoutSeconds`         | Timeout seconds for readinessProbe                               | `5`             |
| `readinessProbe.failureThreshold`       | Failure threshold for readinessProbe                             | `5`             |
| `readinessProbe.successThreshold`       | Success threshold for readinessProbe                             | `1`             |
| `resources.limits`                      | The resources limits for the Sequin containers                   | `{}`            |
| `resources.requests`                    | The requested resources for the Sequin containers                | `{}`            |
| `podSecurityContext.enabled`            | Enabled Sequin pods' Security Context                            | `true`          |
| `podSecurityContext.fsGroup`            | Set Sequin pod's Security Context fsGroup                        | `1001`          |
| `containerSecurityContext.enabled`      | Enabled containers' Security Context                             | `true`          |
| `containerSecurityContext.runAsUser`    | Set containers' Security Context runAsUser                       | `1001`          |
| `containerSecurityContext.runAsNonRoot` | Set container's Security Context runAsNonRoot                    | `true`          |

### Traffic Exposure Parameters

| Name                               | Description                                                      | Value                    |
| ---------------------------------- | ---------------------------------------------------------------- | ------------------------ |
| `service.type`                     | Sequin service type                                              | `LoadBalancer`           |
| `service.ports.http`               | Sequin service HTTP port                                         | `80`                     |
| `service.nodePorts.http`           | Node port for HTTP                                               | `""`                     |
| `service.clusterIP`                | Sequin service Cluster IP                                        | `""`                     |
| `service.loadBalancerIP`           | Sequin service Load Balancer IP                                  | `""`                     |
| `service.loadBalancerSourceRanges` | Sequin service Load Balancer sources                             | `[]`                     |
| `service.externalTrafficPolicy`    | Sequin service external traffic policy                           | `Cluster`                |
| `service.annotations`              | Additional custom annotations for Sequin service                 | `{}`                     |
| `service.extraPorts`               | Extra ports to expose in Sequin service                          | `[]`                     |
| `service.sessionAffinity`          | Session Affinity for Kubernetes service                          | `None`                   |
| `service.sessionAffinityConfig`    | Additional settings for the sessionAffinity                      | `{}`                     |
| `ingress.enabled`                  | Enable ingress record generation for Sequin                      | `false`                  |
| `ingress.pathType`                 | Ingress path type                                                | `ImplementationSpecific` |
| `ingress.apiVersion`               | Force Ingress API version (automatically detected if not set)    | `""`                     |
| `ingress.hostname`                 | Default host for the ingress record                              | `sequin.local`           |
| `ingress.path`                     | Default path for the ingress record                              | `/`                      |
| `ingress.annotations`              | Additional annotations for the Ingress resource                  | `{}`                     |
| `ingress.tls`                      | Enable TLS configuration for the host defined at ingress.hostname | `false`                  |
| `ingress.selfSigned`               | Create a TLS secret for this ingress record using self-signed certificates | `false`                  |
| `ingress.extraHosts`               | An array with additional hostname(s) to be covered with the ingress record | `[]`                     |
| `ingress.extraPaths`               | An array with additional arbitrary paths that may need to be added to the ingress | `[]`                     |
| `ingress.extraTls`                 | TLS configuration for additional hostname(s) to be covered with this ingress record | `[]`                     |
| `ingress.secrets`                  | Custom TLS certificates as secrets                               | `[]`                     |

### PostgreSQL chart configuration

| Name                                          | Description                                                | Value               |
| --------------------------------------------- | ---------------------------------------------------------- | ------------------- |
| `postgresql.enabled`                          | Deploy PostgreSQL subchart                                 | `true`              |
| `postgresql.nameOverride`                     | Override name of the PostgreSQL chart                      | `""`                |
| `postgresql.auth.existingSecret`              | Existing secret containing the password                    | `""`                |
| `postgresql.auth.password`                    | Password for the postgres user (auto-generated if not set) | `""`                |
| `postgresql.auth.username`                    | Username to create when deploying the PostgreSQL chart     | `sequin`            |
| `postgresql.auth.database`                    | Database to create when deploying the PostgreSQL chart     | `sequin`            |
| `postgresql.primary.service.ports.postgresql` | PostgreSQL service port                                    | `5432`              |
| `postgresql.primary.persistence.enabled`      | Enable PostgreSQL Primary data persistence                 | `true`              |
| `postgresql.primary.persistence.size`         | PVC Storage Request for PostgreSQL volume                  | `8Gi`               |

### External Database Configuration

| Name                        | Description                                                         | Value       |
| --------------------------- | ------------------------------------------------------------------- | ----------- |
| `externalDatabase.host`     | Host of an external PostgreSQL instance to connect                  | `""`        |
| `externalDatabase.user`     | User of an external PostgreSQL instance to connect                  | `postgres`  |
| `externalDatabase.password` | Password of an external PostgreSQL instance to connect              | `""`        |
| `externalDatabase.existingSecret` | Secret containing the password of an external PostgreSQL instance | `""`        |
| `externalDatabase.database` | Database inside an external PostgreSQL to connect                   | `sequin`    |
| `externalDatabase.port`     | Port of an external PostgreSQL to connect                           | `5432`      |

### Redis chart configuration

| Name                      | Description                                           | Value      |
| ------------------------- | ----------------------------------------------------- | ---------- |
| `redis.enabled`           | Switch to enable or disable the Redis helm chart      | `true`     |
| `redis.auth.enabled`      | Enable password authentication                        | `false`    |
| `redis.auth.password`     | Redis password                                        | `""`       |
| `redis.auth.existingSecret` | The name of an existing secret with Redis credentials | `""`       |
| `redis.architecture`      | Redis architecture. Allowed values: standalone or replication | `standalone` |

### External Redis Configuration

| Name                       | Description                                                | Value       |
| -------------------------- | ---------------------------------------------------------- | ----------- |
| `externalRedis.host`       | Redis host                                                 | `localhost` |
| `externalRedis.port`       | Redis port number                                          | `6379`      |
| `externalRedis.password`   | Redis password                                             | `""`        |
| `externalRedis.existingSecret` | Name of an existing secret resource containing Redis credentials | `""`        |

## Configuration and installation details

### External Database

To use an external database, set `postgresql.enabled=false` and configure the `externalDatabase` parameters:

```console
postgresql.enabled=false
externalDatabase.host=myexternalhost
externalDatabase.user=myuser
externalDatabase.password=mypassword
externalDatabase.database=mydatabase
externalDatabase.port=5432
```

### External Redis

To use an external Redis instance, set `redis.enabled=false` and configure the `externalRedis` parameters:

```console
redis.enabled=false
externalRedis.host=myexternalhost
externalRedis.port=6379
externalRedis.password=mypassword
```

### Ingress

This chart provides support for Ingress resources. If you have an ingress controller installed on your cluster, such as [nginx-ingress-controller](https://github.com/bitnami/charts/tree/main/bitnami/nginx-ingress-controller) or [contour](https://github.com/bitnami/charts/tree/main/bitnami/contour) you can utilize the ingress controller to serve your application.

To enable Ingress integration, set `ingress.enabled` to `true`. The `ingress.hostname` property can be used to set the host name. The `ingress.tls` parameter can be used to add the TLS configuration for this host.

### Additional environment variables

In case you want to add extra environment variables (useful for advanced operations like custom init scripts), you can use the `extraEnvVars` property.

```yaml
extraEnvVars:
  - name: LOG_LEVEL
    value: error
```

Alternatively, you can use a ConfigMap or a Secret with the environment variables. To do so, use the `extraEnvVarsCM` or the `extraEnvVarsSecret` values.

### Sidecars

If additional containers are needed in the same pod as Sequin (such as additional metrics or logging exporters), they can be defined using the `sidecars` parameter.

```yaml
sidecars:
  - name: your-image-name
    image: your-image
    imagePullPolicy: Always
    ports:
      - name: portname
        containerPort: 1234
```


## Migration from helm-chart-sequin-old (v0.1.0) to helm-chart-sequin-new (v0.2.0)

### Major Changes

The new Sequin Helm chart (v0.2.0) follows the Bitnami chart standard and includes several improvements over the old chart (v0.1.0):

1. **Dependency Management**: Uses Bitnami's PostgreSQL and Redis charts as dependencies
2. **Security Enhancements**: Improved security context settings and secret management
3. **Configuration Options**: More granular configuration options for all components
4. **Resource Management**: Better defaults for resource requests and limits
5. **Ingress Support**: Enhanced ingress configuration with TLS support

### Migration Steps

To migrate from the old chart (v0.1.0) to the new chart (v0.2.0), follow these steps:

1. **Backup Your Data**

   Before migration, backup your PostgreSQL database:

   ```console
   kubectl exec -it <your-postgres-pod> -- pg_dump -U postgres -d sequin > sequin_backup.sql
   ```

2. **Get Current Configuration Values**

   Extract your current configuration values:

   ```console
   helm get values <release-name> > old_values.yaml
   ```

3. **Map Old Values to New Format**

   Create a new values file for the v0.2.0 chart using this mapping:

   | Old Chart Path (v0.1.0) | New Chart Path (v0.2.0) |
   |-------------------------|-------------------------|
   | `sequin.image.repository` | `image.repository` |
   | `sequin.image.tag` | `image.tag` |
   | `sequin.image.pullPolicy` | `image.pullPolicy` |
   | `sequin.service.type` | `service.type` |
   | `sequin.service.port` | `service.ports.http` |
   | `sequin.service.nodePort` | `service.nodePorts.http` |
   | `sequin.config.pgHostname` | Use `postgresql.enabled=true` or `externalDatabase.host` |
   | `sequin.config.pgDatabase` | `postgresql.auth.database` or `externalDatabase.database` |
   | `sequin.config.pgPort` | `postgresql.primary.service.ports.postgresql` or `externalDatabase.port` |
   | `sequin.config.pgUsername` | `postgresql.auth.username` or `externalDatabase.user` |
   | `sequin.config.pgPassword` | `postgresql.auth.password` or `externalDatabase.password` |
   | `sequin.config.pgPoolSize` | `postgresqlPoolSize` |
   | `sequin.config.redisUrl` | Use `redis.enabled=true` or configure `externalRedis.*` |
   | `postgres.image.*` | `postgresql.*` (if using built-in PostgreSQL) |
   | `postgres.service.*` | `postgresql.primary.service.*` |
   | `postgres.config.database` | `postgresql.auth.database` |
   | `postgres.config.username` | `postgresql.auth.username` |
   | `postgres.config.password` | `postgresql.auth.password` |
   | `postgres.persistence.*` | `postgresql.primary.persistence.*` |
   | `redis.image.*` | `redis.*` (if using built-in Redis) |
   | `redis.service.*` | Managed by Redis subchart |
   | `redis.persistence.*` | Managed by Redis subchart |

4. **Uninstall Old Chart**

   ```console
   helm uninstall <release-name>
   ```

   > **IMPORTANT**: If you want to preserve your PVCs, add the `--keep-history` flag

5. **Install New Chart**

   ```console
   helm install <release-name> oci://registry-1.docker.io/sequinstream/sequin -f new_values.yaml
   ```

   Or install from the local directory:

   ```console
   helm install <release-name> ./helm-chart-sequin -f new_values.yaml
   ```

### Example Migration

Old values.yaml (v0.1.0):
```yaml
sequin:
  image:
    repository: sequin/sequin
    tag: latest
  service:
    type: NodePort
    port: 7376
    nodePort: 31376
  config:
    pgHostname: sequin-postgres
    pgDatabase: sequin
    pgPort: 5432
    pgUsername: postgres
    pgPassword: postgres
    pgPoolSize: 20
    redisUrl: "redis://sequin-redis:6379"

postgres:
  persistence:
    enabled: true
    size: 1Gi

redis:
  persistence:
    enabled: true
    size: 1Gi
```

New values.yaml (v0.2.0):
```yaml
image:
  repository: sequin/sequin
  tag: latest

postgresqlPoolSize: 20

service:
  type: NodePort
  ports:
    http: 7376
  nodePorts:
    http: 31376

postgresql:
  enabled: true
  auth:
    username: postgres
    password: postgres
    database: sequin
  primary:
    persistence:
      enabled: true
      size: 1Gi

redis:
  enabled: true
  master:
    persistence:
      enabled: true
      size: 1Gi
```

## Troubleshooting

If you encounter issues during migration:

1. **Database Connection Issues**: Ensure the database connection parameters are correctly mapped
2. **Redis Connection Issues**: Verify Redis connection settings
3. **Persistence Issues**: Check that PVCs are correctly mounted
4. **Service Access Issues**: Verify service configuration and port mappings

For additional help, please:
1. Check the [Sequin documentation](https://github.com/sequinstream/sequin)
2. Open an issue in the GitHub repository

## License

This chart is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
