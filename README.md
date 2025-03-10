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

### Redis Configuration

This chart uses Bitnami's Redis chart as a dependency. For a full list of configuration options, please see the [Bitnami Redis chart documentation](https://github.com/bitnami/charts/tree/main/bitnami/redis).

| Parameter                    | Description                 | Default |
| ---------------------------- | --------------------------- | ------- |
| `redis.enabled`              | Deploy Redis                | `true`  |
| `redis.auth.enabled`         | Enable Redis authentication | `false` |
| `redis.master.service.ports.redis` | Redis port            | `6379`  |

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
postgresql:
  enabled: false

sequin:
  config:
    pgHostname: "my-external-postgres-host"
    pgPort: 5432
    pgUsername: "my-username"
    pgPassword: "my-password"
    pgDatabase: "my-database"
```

### Example: Configuring External Redis

```yaml
redis:
  enabled: false

sequin:
  config:
    redisUrl: "redis://my-external-redis-host:6379"
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
