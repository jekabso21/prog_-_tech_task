# Log Processor

## Overview
The Log Processor formats, labels, and deduplicates incoming logs to prepare them for Loki ingestion. Metadata such as `app`, `env`, `hostname`, and custom labels enhance query performance in Loki and improve log organization.

## Key Functions
- **Parsing and Formatting**: Processes raw log entries and formats them to be Loki-compatible (JSON or Protobuf).
- **Labeling**: Tags logs with relevant metadata to enable efficient querying in Loki. Default labels include `app`, `env`, and `hostname`, with optional custom labels.
- **Deduplication**: Avoids redundant entries by detecting duplicate logs, particularly useful for repetitive sources.

## Configuration Options
```yaml
labels:
  - app: "custom-logger"
  - env: "production"
  - hostname: "server-1"
format: "json"    # Options: "json" or "protobuf"
deduplication: true
```

## Example JSON Format
```json
{
  "timestamp": "2024-11-06T12:34:56Z",
  "app": "custom-logger",
  "env": "production",
  "hostname": "server-1",
  "log": "Sample log message"
}
```

---

### `Batcher_and_Compressor.md`

# Batcher and Compressor

## Overview
The Batcher and Compressor groups logs into batches and compresses them to reduce payload size, optimizing the network transfer to Loki. Batches can be configured based on size or time thresholds.

## Key Functions
- **Batching**: Collects logs based on a maximum batch size or time interval.
- **Compression**: Compresses batched logs using **Snappy** or **GZIP** for efficient network transfer.
- **Encryption (Optional)**: Provides optional encryption for secure data transfer, if required by the deployment environment.

## Configuration Options
```yaml
batch_size: 1000        # Max logs per batch
batch_interval: 5s      # Time interval before sending a batch
compression: "snappy"   # Options: "snappy" or "gzip"
encryption: false       # Optional
```

## Example Workflow
1. Logs are aggregated and passed to the batcher.
2. Once the batch size or interval threshold is met, logs are compressed.
3. Compressed batches are sent to the HTTP Transport for transmission to Loki.