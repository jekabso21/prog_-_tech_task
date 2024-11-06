# Logger Software Architecture

## Overview

The logger software is designed to efficiently aggregate and send log data to a Loki instance, allowing for high-performance logging and streamlined data transfer. This logger provides a structured way to handle logs from various sources, enhance metadata labeling, and ensure optimal compatibility with Loki’s ingestion endpoints.

## Objectives

1. **Efficient Log Aggregation**: Collect logs from multiple sources and formats, including files, stdout, and network sources, for centralized processing.
2. **Scalable Ingestion**: Provide mechanisms to handle high-volume log ingestion with minimal latency.
3. **Flexible Labeling and Filtering**: Allow customizable labeling of logs to improve Loki query performance and data organization.
4. **Optimized Data Transfer**: Implement compression and batching strategies to minimize network usage and maximize throughput.
5. **Configurable and Extensible**: Offer a flexible configuration to adapt to various environments, ensuring compatibility across different deployments.

## Components

### 1. **Log Aggregator**
   - Collects logs from specified sources and processes them for labeling and metadata attachment.
   - Supports multiple input types (e.g., files, stdout, network logs).
   - Filters and processes logs based on severity, source, or other user-defined parameters.

### 2. **Log Processor**
   - Parses raw log entries and attaches metadata such as `app`, `env`, `hostname`, and custom labels as specified.
   - Formats log entries in a Loki-compatible format (JSON or Protobuf).
   - Handles log deduplication to avoid redundant entries, particularly useful for highly repetitive log sources.

### 3. **Batcher and Compressor**
   - Batches logs based on time or size thresholds, grouping entries to optimize transport to Loki.
   - Compresses batches using a lightweight, fast compression algorithm like **Snappy** to balance performance and size.
   - Prepares logs for efficient, secure transfer, with optional encryption if required.

### 4. **HTTP Transport**
   - Handles HTTP/HTTPS connections to Loki’s ingestion endpoint.
   - Manages retry logic for transient errors and implements rate limiting to avoid overloading Loki.
   - Logs successful and failed requests for monitoring and debugging purposes.

### 5. **Configuration Manager**
   - Reads and manages configuration files or environment variables to control settings like:
     - Log sources and paths
     - Label definitions
     - Batch and compression settings
     - Loki server connection details
   - Allows dynamic reconfiguration for flexibility in various environments.

## Workflow

1. **Log Collection**: The Log Aggregator gathers logs from specified sources. Based on configuration, logs are either continuously read (e.g., tailing files) or received as discrete events (e.g., API requests).
   
2. **Processing and Labeling**:
   - The Log Processor formats each log entry to a Loki-compatible JSON or Protobuf structure.
   - Metadata such as `app`, `host`, and custom labels are attached to logs to facilitate efficient querying and filtering within Loki.
   - Deduplication checks are performed, and only unique or significant log entries proceed.

3. **Batching and Compression**:
   - Processed logs are batched based on defined thresholds (e.g., number of entries or time intervals).
   - Batches are compressed using Snappy or a similarly fast algorithm to reduce payload size, optimizing network usage.

4. **Transmission to Loki**:
   - The HTTP Transport module sends batched logs to Loki’s `/loki/api/v1/push` endpoint.
   - Retries are managed for any failed requests, with exponential backoff to avoid overwhelming Loki.
   - Successful transmissions are logged, and any failed ones are recorded for review and potential retransmission.

## Software and Dependencies

1. **Programming Language**: Select a language with strong networking and JSON handling capabilities (e.g., Python, Go, or Node.js).
2. **HTTP/HTTPS Library**: Required for establishing and managing connections to the Loki endpoint.
3. **Compression Library (e.g., Snappy)**: A lightweight, high-performance compression library is essential for log batch compression.
4. **JSON/Protobuf Encoder**: Formats logs in a Loki-compatible structure for efficient transport and querying.

## Technical Details

- **File Formats**:
  - **JSON**: For simplicity and human readability.
  - **Protobuf**: For environments requiring compact, efficient serialization (especially useful in high-load scenarios).

- **Compression**:
  - **Snappy**: Chosen for its speed and lightweight nature, providing a good balance of compression ratio and minimal impact on CPU.
  - **Optional GZIP**: If more compression is required, especially for very large batches, GZIP may be considered.

## Configuration Example

```yaml
loki:
  endpoint: "http://loki-server:3100/loki/api/v1/push"
  batch_size: 1000        # Number of logs per batch
  batch_interval: 5s      # Time interval before sending a batch
  labels:
    - app: "custom-logger"
    - env: "production"
    - hostname: "server-1"
  compression: "snappy"   # Snappy or GZIP
  retry:
    max_retries: 5
    backoff: 2s
log_sources:
  - type: "file"
    path: "/var/log/app.log"
  - type: "stdout"
  - type: "network"
    host: "0.0.0.0"
    port: 9000
```

## Best Practices

1. **Use Meaningful Labels**: Define labels that provide contextual metadata, such as `service`, `hostname`, `env`, to enhance query accuracy.
2. **Optimize Batch Size**: Set a batch size and interval based on log volume to avoid delays or excessive network usage.
3. **Implement Reliable Error Handling**: Ensure that HTTP Transport has retry and backoff mechanisms, particularly in high-volume deployments.
4. **Monitor Performance**: Regularly monitor log processing times, error rates, and batch sizes to optimize performance and resource usage.
