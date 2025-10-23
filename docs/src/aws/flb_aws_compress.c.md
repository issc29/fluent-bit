# src/aws/flb_aws_compress.c Documentation

## Overview

This C source file implements the AWS compression functionality for Fluent Bit. It provides a unified interface for various compression algorithms (gzip, zstd, Arrow, Parquet) that can be used by AWS plugins to compress data before transmission.

## Key Functions

### `flb_aws_compression_get_type`
Maps compression keyword strings to internal compression type identifiers.
- **Parameters**: `compression_keyword` - String representation of compression type
- **Returns**: Integer compression type identifier or -1 on error
- **Supported Types**: "gzip", "zstd", "arrow" (conditional), "parquet" (conditional)

### `flb_aws_compression_compress`
Applies the specified compression algorithm to input data.
- **Parameters**:
  - `compression_type`: Internal compression type identifier
  - `in_data`: Input data to compress
  - `in_len`: Length of input data
  - `out_data`: Output parameter for compressed data buffer
  - `out_len`: Output parameter for size of compressed data
- **Returns**: 0 on success, -1 on error
- **Description**: Delegates to the appropriate compression function based on type

### `flb_aws_compression_b64_truncate_compress`
Compresses data and ensures the base64-encoded result fits within a maximum size.
- **Parameters**:
  - `compression_type`: Internal compression type identifier
  - `max_out_len`: Maximum allowed output length
  - `in_data`: Input data to compress
  - `in_len`: Length of input data
  - `out_data`: Output parameter for base64-encoded compressed data
  - `out_len`: Output parameter for size of base64-encoded data
- **Returns**: 0 on success, -1 on error
- **Description**: Iteratively truncates input data until compressed output fits size limit

## Important Variables and Constants

### Compression Options Structure
```c
struct compression_option {
    int compression_type;
    char *compression_keyword;
    int(*compress)(void *in_data, size_t in_len, void **out_data, size_t *out_len);
};
```
- Maps compression types to their string representations and function pointers
- Conditionally includes Arrow and Parquet options

### Truncation Constants
- `truncation_suffix`: "[Truncated...]" suffix added to truncated data
- `truncation_reduction_percent`: 90% reduction factor for iterative truncation
- `truncation_compression_max_attempts`: Maximum 10 attempts to avoid infinite loops

## Dependencies and Relationships

This file depends on:
- Fluent Bit core utilities (flb_mem.h, flb_log.h, flb_base64.h)
- Compression libraries (flb_gzip.h, flb_zstd.h)
- Arrow compression (conditional, compress.h)
- Standard C libraries (stdint.h)

It is used by:
- AWS output plugins (S3, CloudWatch Logs, etc.)
- Other components needing AWS-compatible compression

## Notable Implementation Details

1. **Plugin Architecture**: Uses function pointer table for extensible compression support
2. **Conditional Compilation**: Arrow and Parquet support only compiled when available
3. **Iterative Truncation**: Sophisticated algorithm to ensure compressed output fits size limits
4. **Base64 Encoding**: Built-in base64 encoding for AWS compatibility
5. **Memory Management**: Proper allocation and deallocation of buffers
6. **Error Handling**: Comprehensive error checking and reporting
7. **Size Calculation**: Accurate base64 buffer size calculation

## Usage Examples

The functions are typically called by AWS output plugins:
```c
#include <fluent-bit/aws/flb_aws_compress.h>

void *compressed_data;
size_t compressed_size;

// Get compression type from keyword
int compression_type = flb_aws_compression_get_type("gzip");

// Compress data
if (flb_aws_compression_compress(compression_type, input_data, input_size, 
                                &compressed_data, &compressed_size) == 0) {
    // Use compressed_data
    // Remember to free compressed_data when done
}

// Compress with size limit and base64 encoding
if (flb_aws_compression_b64_truncate_compress(compression_type, max_size,
                                             input_data, input_size,
                                             &compressed_data, &compressed_size) == 0) {
    // Use base64-encoded compressed_data
    // Remember to free compressed_data when done
}
```