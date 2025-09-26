# Walrus Chart Components

The walrus chart supports the main node component (always enabled) and optional aggregator and publisher components:

## Components

### Node (Always Enabled)
- **Always enabled**: Node component is always deployed
- **Port**: 9185 (configurable via `walrus.config.public_port`)
- **Purpose**: Main walrus node functionality

### Aggregator (Optional)
- **Enabled**: `aggregator.enabled: true`
- **Port**: 9000 (configurable via `aggregator.public_port`)
- **Metrics Port**: 27182 (configurable via `aggregator.metrics_port`)
- **Purpose**: Aggregator functionality for caching and load distribution

### Publisher (Optional)
- **Enabled**: `publisher.enabled: true`
- **Port**: 9001 (configurable via `publisher.public_port`)
- **Metrics Port**: 27183 (configurable via `publisher.metrics_port`)
- **Purpose**: Publisher functionality for content distribution

## Configuration

Each component has its own configuration section in `values.yaml`:

```yaml
components:
  aggregator:
    enabled: false
  publisher:
    enabled: false

walrus:
  # Node configuration (always enabled)
  config:
    name: name
    public_host: hostname
    public_port: 9185
    # ... other node-specific config

  # Aggregator configuration
  aggregator:
    config:
      name: aggregator
      public_host: aggregator-hostname
      public_port: 9186
      # ... other aggregator-specific config

  # Publisher configuration
  publisher:
    config:
      name: publisher
      public_host: publisher-hostname
      public_port: 9187
      # ... other publisher-specific config
```

## Usage Examples

### Deploy only the node (default behavior)
```yaml
components:
  aggregator:
    enabled: false
  publisher:
    enabled: false
```

### Deploy node and aggregator
```yaml
components:
  aggregator:
    enabled: true
  publisher:
    enabled: false
```

### Deploy all components
```yaml
components:
  aggregator:
    enabled: true
  publisher:
    enabled: true
```

### Deploy node and publisher
```yaml
components:
  aggregator:
    enabled: false
  publisher:
    enabled: true
```

## Resources Created

- **Node** (always created):
  - StatefulSet for the deployment
  - Service for the main port
  - Service for metrics port (9184)
  - ConfigMap for configuration
  - Persistent volume claims for data storage
  - ServiceAccount (shared with other components)

- **Aggregator** (when enabled):
  - Deployment (stateless)
  - Service for the main port
  - Service for metrics port (27182)
  - Uses same ConfigMap as node
  - Uses same ServiceAccount as node
  - Additional label: `role: aggregator`

- **Publisher** (when enabled):
  - Deployment (stateless)
  - Service for the main port
  - Service for metrics port (27183)
  - Uses same ConfigMap as node
  - Uses same ServiceAccount as node
  - Additional label: `role: publisher`
  - Additional wallets volume for sub-wallets

## Service Account & Labels

All components share the same ServiceAccount for simplified RBAC management. Components are distinguished by labels:

- **Node**: `app.kubernetes.io/component: node` (default)
- **Aggregator**: `app.kubernetes.io/component: aggregator`
- **Publisher**: `app.kubernetes.io/component: publisher`

All components share these common labels:
- `app.kubernetes.io/name: walrus`
- `app.kubernetes.io/instance: <release-name>`
- `app.kubernetes.io/managed-by: Helm`

## Ports

- **Node**: 9185 (configurable via `walrus.config.public_port`)
- **Aggregator**: 9000 (configurable via `walrus.aggregator.config.public_port`)
- **Publisher**: 9001 (configurable via `walrus.publisher.config.public_port`)
- **Node Metrics**: 9184 (standard metrics port)
- **Aggregator Metrics**: 27182 (configurable via `walrus.aggregator.config.metrics_port`)
- **Publisher Metrics**: 27183 (configurable via `walrus.publisher.config.metrics_port`)
