# Log Aggregator

## Overview
The Log Aggregator is responsible for gathering logs from multiple sources, such as files, stdout, and network inputs, consolidating them for processing and transmission to Loki. It provides the foundation for scalable, centralized log collection and supports filtering by log severity, source, and custom-defined parameters.

## Key Functions
- **Source Integration**: The Log Aggregator can monitor multiple sources:
  - **File-based Logs**: Tails logs from files (e.g., `/var/log/app.log`).
  - **Stdout Logs**: Captures log messages sent to standard output.
  - **Network Logs**: Listens on specified network ports for incoming logs.
- **Filtering**: Filters logs based on severity, allowing prioritized log processing (e.g., `INFO`, `WARN`, `ERROR`).
- **Event-Driven Collection**: For sources generating discrete log events (e.g., network logs), the aggregator captures logs in real-time.

## Configuration Options
```yaml
log_sources:
  - type: "file"
    path: "/var/log/app.log"
  - type: "stdout"
  - type: "network"
    host: "0.0.0.0"
    port: 9000
severity_levels: ["INFO", "WARN", "ERROR"]
```