# Prisma Studio Helm Chart

A Helm chart for deploying Prisma Studio, a visual database editor for your Prisma schema, to Kubernetes.

## Introduction

This Helm chart deploys Prisma Studio in a Kubernetes cluster. Prisma Studio provides a modern interface for viewing and editing data in your database.

## Prerequisites

- Kubernetes 1.16+
- Helm 3.0+
- A PostgreSQL database accessible from your Kubernetes cluster

## Installing the Chart

Add the repository (if hosted):

```bash
git clone  <this-repo>
```

To install the chart with the release name `prisma-studio`:

```bash
helm install prisma-studio my-repo/prisma-studio
```

Or from a local directory:

```bash
helm install prisma-studio ./prisma-studio
```

## Configuration

The following table lists the configurable parameters of the Prisma Studio chart and their default values.

| Parameter                     | Description                                         | Default                        |
| ----------------------------- | --------------------------------------------------- | ------------------------------ |
| `image.repository`            | Image repository                                    | `timothyjmiller/prisma-studio` |
| `image.tag`                   | Image tag                                           | `latest`                       |
| `image.pullPolicy`            | Image pull policy                                   | `Always`                       |
| `replicaCount`                | Number of replicas                                  | `2`                            |
| `database.type`               | Database type                                       | `postgresql`                   |
| `database.name.value`         | Database name                                       | `""`                           |
| `database.host.value`         | Database host                                       | `""`                           |
| `database.user.value`         | Database username                                   | `""`                           |
| `database.user.valueFrom`     | Reference to database username secret               | `{}`                           |
| `database.password.value`     | Database password (not recommended - use valueFrom) | `""`                           |
| `database.password.valueFrom` | Reference to database password secret               | `{}`                           |
| `service.type`                | Kubernetes Service type                             | `ClusterIP`                    |
| `service.port`                | Service port                                        | `80`                           |
| `service.targetPort`          | Port exposed by the container                       | `5555`                         |
| `service.protocol`            | Service protocol                                    | `TCP`                          |
| `resources.requests.cpu`      | CPU resource request                                | `20m`                          |
| `resources.requests.memory`   | Memory resource request                             | `55M`                          |

### Database Configuration

You can configure database credentials directly in values or reference existing secrets:

```yaml
# Direct values
database:
  name:
    value: "mydatabase"
  host:
    value: "postgresql-host"
  user:
    value: "postgres"
  password:
    value: "password123"  # Not recommended for production

# Using existing secrets
database:
  name:
    value: "mydatabase"
  host:
    value: "postgresql-host"
  user:
    valueFrom:
      secretKeyRef:
        name: db-credentials
        key: username
  password:
    valueFrom:
      secretKeyRef:
        name: db-credentials
        key: password
```

## Example: Connecting to an External Database

Create a values file (`values-external-db.yaml`):

```yaml
replicaCount: 1

database:
  name:
    value: "myapp"
  host:
    value: "postgres.example.com"
  user:
    valueFrom:
      secretKeyRef:
        name: postgres-credentials
        key: username
  password:
    valueFrom:
      secretKeyRef:
        name: postgres-credentials
        key: password

service:
  type: ClusterIP
```

Create the required secret:

```bash
kubectl create secret generic postgres-credentials \
  --from-literal=username=dbuser \
  --from-literal=password=dbpassword
```

Install the chart:

```bash
helm install prisma-studio ./prisma-studio -f values-external-db.yaml
```

## Accessing Prisma Studio

After deploying, you can access Prisma Studio by port-forwarding the service:

```bash
kubectl port-forward svc/prisma-studio-svc 5555:80
```

Then open your browser at `http://localhost:5555`

## Uninstalling the Chart

To uninstall/delete the `prisma-studio` deployment:

```bash
helm uninstall prisma-studio
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This chart is licensed under the MIT License.
