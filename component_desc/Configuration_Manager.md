# Configuration Manager

## Overview
The Configuration Manager reads and manages configuration files or environment variables, offering flexible settings for log sources, labels, batching, compression, and Loki connection details. This component supports dynamic reconfiguration, allowing updates to take effect without needing a restart.

## Key Functions
- **Config Loading**: Reads configurations from YAML files or environment variables.
- **Dynamic Reconfiguration**: Allows changes to configuration at runtime without requiring a restart.
- **Environment Support**: Enables configuration overrides for different environments (e.g., development, production).

## Configuration Structure
```yaml
loki:
  endpoint: "http://loki-server:3100/loki/api/v1/push"
batcher:
  batch_size: 1000
  batch_interval: 5s
  compression: "snappy"
labels:
  - app: "custom-logger"
  - env: "production"
log_sources:
  - type: "file"
    path: "/var/log/app.log"
retry:
  max_retries: 5
  backoff: 2s
```

## Example Usage
1. Configuration Manager reads `config.yaml` at startup.
2. If any values are changed, the manager updates the relevant components (e.g., changing `batch_size` immediately impacts the batcher).
3. Configurations can be overridden by environment variables if needed for different deployment environments.
