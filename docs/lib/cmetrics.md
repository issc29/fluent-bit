# CMetrics

## Overview

CMetrics is a standalone C library to create and maintain a context of different sets of metrics with labels support. It provides implementations for common metric types used in monitoring and observability systems:

- Counters
- Gauges
- Histograms
- Summaries
- Untyped metrics

The library is heavily based on the Go Prometheus Client API design and supports various encoding/decoding formats for interoperability with other monitoring systems.

## Key Methods/Functions

### Library Initialization

- `cmt_initialize()` - Initialize the CMetrics library
- `cmt_create()` - Create a new CMetrics context
- `cmt_destroy()` - Destroy a CMetrics context and release all resources
- `cmt_label_add()` - Add a static label to the context
- `cmt_version()` - Get the library version string

### Counter Operations

- `cmt_counter_create()` - Create a new counter metric
- `cmt_counter_destroy()` - Destroy a counter metric
- `cmt_counter_inc()` - Increment a counter by a value
- `cmt_counter_get_val()` - Get the current value of a counter

### Gauge Operations

- `cmt_gauge_create()` - Create a new gauge metric
- `cmt_gauge_destroy()` - Destroy a gauge metric
- `cmt_gauge_set()` - Set the value of a gauge
- `cmt_gauge_add()` - Add a value to a gauge
- `cmt_gauge_sub()` - Subtract a value from a gauge
- `cmt_gauge_get_val()` - Get the current value of a gauge

### Histogram Operations

- `cmt_histogram_create()` - Create a new histogram metric
- `cmt_histogram_destroy()` - Destroy a histogram metric
- `cmt_histogram_observe()` - Observe a value in the histogram
- `cmt_histogram_get_buckets()` - Get the bucket configuration

### Summary Operations

- `cmt_summary_create()` - Create a new summary metric
- `cmt_summary_destroy()` - Destroy a summary metric
- `cmt_summary_observe()` - Observe a value in the summary

### Encoding/Decoding Functions

- `cmt_encode_prometheus()` - Encode metrics in Prometheus text format
- `cmt_encode_msgpack()` - Encode metrics in MessagePack format
- `cmt_encode_opentelemetry()` - Encode metrics in OpenTelemetry format
- `cmt_encode_prometheus_remote_write()` - Encode metrics for Prometheus remote write
- `cmt_decode_prometheus()` - Decode metrics from Prometheus text format
- `cmt_decode_msgpack()` - Decode metrics from MessagePack format
- `cmt_decode_opentelemetry()` - Decode metrics from OpenTelemetry format
- `cmt_decode_prometheus_remote_write()` - Decode metrics from Prometheus remote write format

### Utility Functions

- `cmt_info_create()` - Create an info metric
- `cmt_untyped_create()` - Create an untyped metric
- `cmt_filter_create()` - Create a filter for metrics
- `cmt_filter_add_metric()` - Add a metric to a filter

## Important Usage Notes

1. **Metric Types**: CMetrics supports all standard metric types with labels for dimensional data modeling.

2. **Labels**: Labels are key-value pairs that can be added to metrics to provide additional dimensions for querying and aggregation.

3. **Thread Safety**: The library is designed to be thread-safe for concurrent metric updates, though applications should be aware of potential contention on frequently updated metrics.

4. **Memory Management**: CMetrics handles its own memory management, but users should properly destroy contexts and metrics when no longer needed.

5. **Encoding Formats**: The library supports multiple encoding formats for interoperability with various monitoring backends and protocols.

6. **Aggregation Types**: Metrics can be configured with different aggregation types (delta or cumulative) depending on the use case.

7. **Performance**: The library is optimized for high-performance metric collection with minimal overhead for metric updates.

8. **Compatibility**: Designed to be compatible with Prometheus and OpenTelemetry ecosystems.

## Example Usage

```c
// Initialize the library
cmt_initialize();

// Create a context
struct cmt *cmt = cmt_create();

// Add a static label
cmt_label_add(cmt, "instance", "localhost");

// Create a counter
struct cmt_counter *counter = cmt_counter_create(cmt, "http_requests_total", 
                                                  "Total HTTP requests", 0, NULL);

// Increment the counter
cmt_counter_inc(counter, 1);

// Create a gauge
struct cmt_gauge *gauge = cmt_gauge_create(cmt, "memory_usage_bytes", 
                                           "Current memory usage", 0, NULL);

// Set the gauge value
cmt_gauge_set(gauge, get_current_memory_usage(), 0, NULL);

// Encode metrics in Prometheus format
char *output;
size_t output_size;
int ret = cmt_encode_prometheus(cmt, &output, &output_size);

// Clean up
cmt_destroy(cmt);
```