# Sequin Helm Chart

This Helm chart deploys [Sequin](https://github.com/sequinstream/sequin) along with its required PostgreSQL and Redis dependencies on a Kubernetes cluster.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- PV provisioner support in the underlying infrastructure
- kubectl configured to communicate with your Kubernetes cluster

## Installation

```bash
# Add the repository
helm repo add sequin https://sequinstream.github.io/helm-chart-sequin

# Add the Bitnami repository (required for dependencies)
helm repo add bitnami https://charts.bitnami.com/bitnami

# Update Helm repositories
helm repo update

# Install the chart with the release name "sequin"
helm install sequin sequin/sequin
```

You can now access Sequin at `http://<node-ip>:31376` (`http://localhost:31376` if you are running locally).

## Migration Guide

### Migrating from v0.1.0 to Current Version

The current version of this chart has replaced the built-in PostgreSQL and Redis deployments with Bitnami's PostgreSQL and Redis charts as dependencies. If you're upgrading from v0.1.0, follow these steps to ensure a smooth migration:

#### 1. Back Up Your Data

Before migrating, back up your PostgreSQL data:

```bash
# Get the PostgreSQL pod name
POSTGRES_POD=$(kubectl get pods -l app=<release-name>-postgres -o jsonpath='{.items[0].metadata.name}')

# Back up the database
kubectl exec $POSTGRES_POD -- pg_dump -U postgres sequin > sequin_backup.sql
```

#### 2. Update Your Values File

Update your values file to match the new configuration structure:

**Old Structure (v0.1.0):**
```yaml
postgres:
  image:
    repository: postgres
    tag: "16"
  service:
    type: ClusterIP
    port: 5432
  config:
    database: sequin
    username: postgres
    password: postgres
  persistence:
    enabled: true
    size: 1Gi

redis:
  image:
    repository: redis
    tag: "7"
  service:
    type: ClusterIP
    port: 6379
  persistence:
    enabled: true
    size: 1Gi
```

**New Structure (Current):**
```yaml
postgresql:
  enabled: true
  auth:
    username: postgres
    password: postgres
    database: sequin
  primary:
    service:
      ports:
        postgresql: 5432
  persistence:
    enabled: true
    size: 1Gi

redis:
  enabled: true
  auth:
    enabled: false
  master:
    service:
      ports:
        redis: 6379
  persistence:
    enabled: true
    size: 1Gi
```

#### 3. Update Sequin Configuration

Update the Sequin configuration to use the new service names:

**Old Configuration:**
```yaml
sequin:
  config:
    pgHostname: sequin-postgres
    redisUrl: "redis://sequin-redis:6379"
```

**New Configuration:**
```yaml
sequin:
  config:
    pgHostname: "{{ .Release.Name }}-postgresql"
    redisUrl: "redis://{{ .Release.Name }}-redis-master:6379"
```

#### 4. Add Bitnami Repository and Upgrade

```bash
# Add Bitnami repository
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Upgrade your release
helm upgrade <release-name> . -f your-values.yaml
```

#### 5. Restore Your Data (If Needed)

If you need to restore your data to the new PostgreSQL instance:

```bash
# Get the new PostgreSQL pod name
POSTGRES_POD=$(kubectl get pods -l app.kubernetes.io/name=postgresql,app.kubernetes.io/instance=<release-name> -o jsonpath='{.items[0].metadata.name}')

# Copy the backup file to the pod
kubectl cp sequin_backup.sql $POSTGRES_POD:/tmp/

# Restore the database
kubectl exec $POSTGRES_POD -- psql -U postgres -d sequin -f /tmp/sequin_backup.sql
```

#### 6. Verify the Migration

Verify that Sequin is working correctly with the new PostgreSQL and Redis instances:

```bash
# Check if Sequin pods are running
kubectl get pods -l app=<release-name>-sequin

# Check logs for any errors
kubectl logs -l app=<release-name>-sequin
```

#### 7. Troubleshooting Common Migration Issues

**Issue: PostgreSQL Connection Errors**

If Sequin can't connect to PostgreSQL, verify the service name is correct:

```bash
# Check if the PostgreSQL service exists
kubectl get svc | grep postgresql

# Verify the connection from a temporary pod
kubectl run postgres-client --rm --tty -i --restart='Never' --image postgres:16 --command -- psql -h <release-name>-postgresql -U postgres -d sequin
```

**Issue: Redis Connection Errors**

If Sequin can't connect to Redis, verify the service name is correct:

```bash
# Check if the Redis service exists
kubectl get svc | grep redis

# Verify the connection from a temporary pod
kubectl run redis-client --rm --tty -i --restart='Never' --image redis:7 --command -- redis-cli -h <release-name>-redis-master ping
```

**Issue: Persistent Volume Claims**

If you encounter PVC issues during migration:

```bash
# Check PVC status
kubectl get pvc

# If needed, you can manually create PVCs before upgrading
kubectl apply -f postgresql-pvc.yaml
kubectl apply -f redis-pvc.yaml
```

## Configuration

The following table lists the configurable parameters of the Sequin chart and their default values:

### Sequin Configuration

| Parameter                 | Description             | Default         |
| ------------------------- | ----------------------- | --------------- |
| `sequin.image.repository` | Sequin image repository | `sequin/sequin` |
| `sequin.image.tag`        | Sequin image tag        | `latest`        |
| `sequin.image.pullPolicy` | Image pull policy       | `Always`        |
| `sequin.service.type`     | Kubernetes service type | `NodePort`      |
| `sequin.service.nodePort` | External access port    | `31376`         |

### PostgreSQL Configuration

This chart uses Bitnami's PostgreSQL chart as a dependency. For a full list of configuration options, please see the [Bitnami PostgreSQL chart documentation](https://github.com/bitnami/charts/tree/main/bitnami/postgresql).

| Parameter                        | Description                      | Default    |
| -------------------------------- | -------------------------------- | ---------- |
| `postgresql.enabled`             | Deploy PostgreSQL                | `true`     |
| `postgresql.auth.username`       | PostgreSQL username              | `postgres` |
| `postgresql.auth.password`       | PostgreSQL password              | `postgres` |
| `postgresql.auth.database`       | PostgreSQL database name         | `sequin`   |
| `postgresql.primary.service.ports.postgresql` | PostgreSQL port     | `5432`     |

### External PostgreSQL Configuration

When `postgresql.enabled` is set to `false`, the following external PostgreSQL configuration is used:

| Parameter                        | Description                      | Default    |
| -------------------------------- | -------------------------------- | ---------- |
| `externalPostgresql.host`        | External PostgreSQL host         | `""`       |
| `externalPostgresql.port`        | External PostgreSQL port         | `5432`     |
| `externalPostgresql.username`    | External PostgreSQL username     | `postgres` |
| `externalPostgresql.password`    | External PostgreSQL password     | `""`       |
| `externalPostgresql.database`    | External PostgreSQL database name| `sequin`   |
| `externalPostgresql.poolSize`    | Connection pool size             | `20`       |
| `externalPostgresql.ssl`         | Use SSL for PostgreSQL connection| `false`    |

### Redis Configuration

This chart uses Bitnami's Redis chart as a dependency. For a full list of configuration options, please see the [Bitnami Redis chart documentation](https://github.com/bitnami/charts/tree/main/bitnami/redis).

| Parameter                    | Description                 | Default |
| ---------------------------- | --------------------------- | ------- |
| `redis.enabled`              | Deploy Redis                | `true`  |
| `redis.auth.enabled`         | Enable Redis authentication | `false` |
| `redis.master.service.ports.redis` | Redis port            | `6379`  |

### External Redis Configuration

When `redis.enabled` is set to `false`, the following external Redis configuration is used:

| Parameter                  | Description                 | Default |
| -------------------------- | --------------------------- | ------- |
| `externalRedis.host`       | External Redis host         | `""`    |
| `externalRedis.port`       | External Redis port         | `6379`  |
| `externalRedis.password`   | External Redis password     | `""`    |
| `externalRedis.ssl`        | Use SSL for Redis connection| `false` |
| `externalRedis.database`   | Redis database index        | `0`     |

## Accessing Services

After deploying the chart, you can access the services at:

- Sequin: External access via `http://<node-ip>:31376` (`http://localhost:31376` if you are running locally)
- PostgreSQL: Internal access via `<release-name>-postgresql:5432`
- Redis: Internal access via `<release-name>-redis-master:6379`

## Persistence

Both PostgreSQL and Redis use persistent storage by default through their respective Bitnami charts. You can configure persistence options through the Bitnami chart values.

## Customization

To override the default values, create a file called `custom-values.yaml` and specify any values you want to override. Then install the chart using:

```bash
helm install sequin . -f custom-values.yaml
```

### Example: Configuring External PostgreSQL

```yaml
# Disable the built-in PostgreSQL
postgresql:
  enabled: false

# Configure external PostgreSQL
externalPostgresql:
  host: "my-external-postgres-host"
  port: 5432
  username: "my-username"
  password: "my-password"
  database: "my-database"
  poolSize: 20
  ssl: false
```

### Example: Configuring External Redis

```yaml
# Disable the built-in Redis
redis:
  enabled: false

# Configure external Redis
externalRedis:
  host: "my-external-redis-host"
  port: 6379
  password: "my-redis-password"
  database: 0
  ssl: false
```

## Uninstalling the Chart

To uninstall the chart:

```bash
helm uninstall sequin
```

## Development

To contribute to this chart:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This chart is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

If you have any questions or need help, please:

1. Check the [documentation](link-to-docs)
2. Open an issue in the GitHub repository
