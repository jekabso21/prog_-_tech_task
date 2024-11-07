# HTTP Transport

## Overview
The HTTP Transport handles the reliable transmission of batched log data to Loki's ingestion endpoint. It manages retry logic, rate limiting, and logs the status of successful and failed transmissions.

## Key Functions
- **Connection Management**: Manages HTTP/HTTPS connections to the Loki `/loki/api/v1/push` endpoint.
- **Retry Logic**: Retries failed transmissions with exponential backoff.
- **Rate Limiting**: Prevents overloading Loki by limiting request frequency.
- **Error Logging**: Logs status for successful and failed requests for monitoring.

## Configuration Options
```yaml
loki:
  endpoint: "http://loki-server:3100/loki/api/v1/push"
retry:
  max_retries: 5
  backoff: 2s          # Exponential backoff starting at 2 seconds
rate_limit: 100        # Max requests per minute
```

## Example Workflow
1. Batches from the compressor are sent to the HTTP Transport module.
2. On network or server errors, the transport retries up to the max retries, with backoff.
3. Logs the result of each transmission for monitoring.