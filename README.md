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

| Parameter                   | Description                 | Default    |
| --------------------------- | --------------------------- | ---------- |
| `postgres.image.repository` | PostgreSQL image repository | `postgres` |
| `postgres.image.tag`        | PostgreSQL image tag        | `16`       |
| `postgres.service.type`     | Kubernetes service type     | `NodePort` |
| `postgres.service.nodePort` | External access port        | `30432`    |
| `postgres.config.database`  | PostgreSQL database name    | `sequin`   |
| `postgres.persistence.size` | PVC size for PostgreSQL     | `1Gi`      |

### Redis Configuration

| Parameter                | Description             | Default    |
| ------------------------ | ----------------------- | ---------- |
| `redis.image.repository` | Redis image repository  | `redis`    |
| `redis.image.tag`        | Redis image tag         | `7`        |
| `redis.service.type`     | Kubernetes service type | `NodePort` |
| `redis.service.nodePort` | External access port    | `30379`    |
| `redis.persistence.size` | PVC size for Redis      | `1Gi`      |

## Accessing Services

After deploying the chart, you can access the services at:

- Sequin: Internal access via `sequin-sequin:7376`
- PostgreSQL: External access via `localhost:30432`
- Redis: External access via `localhost:30379`

## Persistence

The chart mounts persistent volumes for both PostgreSQL and Redis. The default size is 1Gi for each service. You can modify this in the `values.yaml` file.

## Customization

To override the default values, create a file called `custom-values.yaml` and specify any values you want to override. Then install the chart using:

```bash
helm install sequin . -f custom-values.yaml
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
