# Test Cases for Custom Logger

## 1. Log Aggregator

### 1.1 File Log Collection
- **Test Case**: Verify that the logger collects logs from specified file sources.
- **Steps**:
  1. Configure the logger to monitor a file (e.g., `/var/log/test.log`).
  2. Append new log entries to the file.
- **Expected Result**: Logger detects and processes new entries from the file.
  
### 1.2 Stdout Log Collection
- **Test Case**: Verify that the logger collects logs from stdout.
- **Steps**:
  1. Configure the logger to capture stdout output.
  2. Generate log messages through stdout.
- **Expected Result**: Logger captures and processes stdout log entries correctly.

### 1.3 Network Log Collection
- **Test Case**: Verify that the logger collects logs sent over the network.
- **Steps**:
  1. Configure the logger to listen on a network port (e.g., `9000`).
  2. Send log entries to the configured port.
- **Expected Result**: Logger captures and processes incoming log entries over the network.

## 2. Log Processor

### 2.1 Labeling of Log Entries
- **Test Case**: Verify that logs are labeled correctly based on configuration.
- **Steps**:
  1. Set up labels (e.g., `app: "test-app"`, `env: "test"`) in the configuration.
  2. Send a log entry through any source.
- **Expected Result**: Log entries include the configured labels.

### 2.2 Log Deduplication
- **Test Case**: Verify that duplicate log entries are not sent to Loki.
- **Steps**:
  1. Send identical log entries consecutively.
- **Expected Result**: The logger sends only one instance of each unique log to Loki.

### 2.3 JSON and Protobuf Formatting
- **Test Case**: Verify that logs are formatted in JSON or Protobuf as per configuration.
- **Steps**:
  1. Configure log output format as JSON or Protobuf.
  2. Send a log entry and inspect the formatted result.
- **Expected Result**: Log entry format matches the selected configuration.

## 3. Batcher and Compressor

### 3.1 Log Batching by Size
- **Test Case**: Verify that logs are batched based on the configured size.
- **Steps**:
  1. Set `batch_size` to a specified number of logs.
  2. Send multiple logs until the batch threshold is met.
- **Expected Result**: Logs are sent in a single batch once the batch size is reached.

### 3.2 Log Batching by Time Interval
- **Test Case**: Verify that logs are batched based on the configured time interval.
- **Steps**:
  1. Set `batch_interval` to a specified duration.
  2. Send logs continuously and check transmission timing.
- **Expected Result**: Logs are batched and sent after the time interval is reached.

### 3.3 Compression
- **Test Case**: Verify that log batches are compressed using the selected compression method.
- **Steps**:
  1. Configure `compression` as Snappy or GZIP.
  2. Send logs and inspect the compressed batch data.
- **Expected Result**: Log batches are compressed with the selected method.

## 4. HTTP Transport

### 4.1 Log Transmission to Loki
- **Test Case**: Verify that the logger successfully sends log batches to the Loki endpoint.
- **Steps**:
  1. Configure Loki endpoint and send logs.
  2. Check Loki for received logs.
- **Expected Result**: Logs appear in Loki, matching the sent data.

### 4.2 Retry on Failed Transmission
- **Test Case**: Verify that the logger retries sending logs if the transmission fails.
- **Steps**:
  1. Configure the Loki endpoint incorrectly to simulate failure.
  2. Send a log entry.
- **Expected Result**: Logger retries sending logs based on the retry policy.

### 4.3 Exponential Backoff
- **Test Case**: Verify that retries use exponential backoff on repeated transmission failures.
- **Steps**:
  1. Set a short retry interval and misconfigure the Loki endpoint.
  2. Observe retry intervals between failed attempts.
- **Expected Result**: Retry intervals increase exponentially with each failure.

## 5. Configuration Manager

### 5.1 Dynamic Configuration Reload
- **Test Case**: Verify that the logger reloads configuration without a restart.
- **Steps**:
  1. Change configuration settings (e.g., labels or endpoint).
  2. Send logs and verify that the changes take effect.
- **Expected Result**: Logger reflects updated configuration without needing a restart.

### 5.2 Invalid Configuration Handling
- **Test Case**: Verify that the logger handles invalid configuration gracefully.
- **Steps**:
  1. Introduce an error in the configuration (e.g., unsupported label format).
  2. Start the logger.
- **Expected Result**: Logger logs an error and continues with default or previous valid settings.

## 6. Performance and Load Testing

### 6.1 High-Volume Log Ingestion
- **Test Case**: Test the logger’s ability to handle high log volume without errors or significant latency.
- **Steps**:
  1. Simulate a high rate of log entries.
  2. Monitor performance metrics and log processing time.
- **Expected Result**: Logger processes logs within acceptable time limits and avoids memory or CPU spikes.

### 6.2 Network Latency and Resilience
- **Test Case**: Verify the logger’s performance under varying network conditions.
- **Steps**:
  1. Simulate network latency or occasional connection drops.
  2. Observe log transmission and retry behavior.
- **Expected Result**: Logger continues to transmit logs as network conditions stabilize.

### 6.3 Memory and Resource Usage
- **Test Case**: Verify that the logger maintains stable memory and CPU usage under normal and high loads.
- **Steps**:
  1. Run logger with continuous log generation.
  2. Monitor memory and CPU usage.
- **Expected Result**: Resource usage remains stable without significant spikes or leaks.