# Batcher and Compressor

## Overview

The Batcher and Compressor component is responsible for grouping log entries into batches and compressing them for efficient transport to Loki. This component optimizes network utilization by reducing the number of requests and minimizing payload sizes. Batching can be configured based on a maximum number of logs per batch or a set time interval. Compressed batches are then forwarded to the HTTP Transport component for transmission.

## Key Functions

### 1. **Batching**
   - Groups log entries into batches based on the configured batch size or time interval.
   - Improves efficiency by reducing the frequency of network requests to Loki.
   - Ensures that log data remains manageable even under high-throughput scenarios.

### 2. **Compression**
   - Compresses batched log entries using lightweight algorithms like **Snappy** or **GZIP**.
   - Reduces payload sizes, optimizing network performance and reducing bandwidth consumption.
   - Balances compression speed and resource usage, ensuring minimal impact on system performance.

### 3. **Optional Encryption**
   - Provides optional encryption to secure log data during transport.
   - Ensures data privacy for sensitive environments, such as those with regulatory compliance needs.

## Workflow

1. **Log Collection**: Logs collected from various sources are forwarded to the Batcher and Compressor component.
2. **Batch Creation**:
   - Logs are grouped based on the batch size (number of logs per batch) or time interval (duration before sending a batch).
   - Once a batch meets the size or time threshold, it’s prepared for compression.
3. **Compression**:
   - The batch is compressed using the configured compression algorithm.
   - Compression options include **Snappy** (for speed) and **GZIP** (for higher compression ratios).
4. **Transfer to HTTP Transport**:
   - Compressed batches are sent to the HTTP Transport component for delivery to Loki.
   - Logs the batch status for monitoring and debugging.

## Configuration Options

Below is an example configuration snippet for the Batcher and Compressor component:

```yaml
batcher:
  batch_size: 1000         # Maximum number of logs in each batch
  batch_interval: 5s       # Time interval (in seconds) to wait before sending a batch
  compression: "snappy"    # Compression options: "snappy" or "gzip"
  encryption: false        # Optional; enables encryption if set to true
```

### Explanation of Configuration Options

- **`batch_size`**: Defines the maximum number of logs in each batch. Once this limit is reached, the batch is processed and sent.
- **`batch_interval`**: Specifies the maximum time to wait before sending a batch, regardless of batch size. This ensures that logs are transmitted even during low-volume periods.
- **`compression`**: Sets the compression algorithm to use:
  - **Snappy**: Fast and lightweight, providing a good balance of speed and compression ratio.
  - **GZIP**: Offers higher compression but may impact performance slightly more than Snappy.
- **`encryption`**: When enabled, encrypts the batch before sending it to the HTTP Transport. Useful for environments requiring data confidentiality.

## Example Workflow

Here’s how the Batcher and Compressor component operates in a typical scenario:

1. **Initial Collection**: Logs from different sources accumulate in the batcher.
2. **Batch Creation**: 
   - If `batch_size` is reached, a batch is created immediately.
   - If `batch_interval` elapses before `batch_size` is reached, the available logs form a batch.
3. **Compression**: The batch is compressed with Snappy or GZIP, as configured.
4. **Transfer to Transport**: The compressed batch is sent to the HTTP Transport component, ready for transmission to Loki.

## Compression Algorithms

- **Snappy**: 
  - Fast compression with a moderate compression ratio.
  - Ideal for high-throughput environments where performance is a priority.
- **GZIP**:
  - Higher compression ratio but with a slightly slower performance than Snappy.
  - Suitable for deployments where network bandwidth is limited and data size is a priority.

## Best Practices

1. **Optimize Batch Size and Interval**: Configure `batch_size` and `batch_interval` based on log volume and available network resources to minimize latency and network usage.
2. **Choose Compression Based on Requirements**: 
   - For high-speed environments, use Snappy for faster compression with minimal CPU impact.
   - Use GZIP if a higher compression ratio is needed, and network efficiency is a priority.
3. **Monitor Batch and Compression Performance**: Regularly monitor the number of logs per batch and compression times to adjust settings as needed, ensuring efficient resource use.