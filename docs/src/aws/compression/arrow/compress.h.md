# src/aws/compression/arrow/compress.h Documentation

## Overview

This C header file defines the public interface for Arrow-based compression functionality in Fluent Bit's AWS integration. It provides function declarations for converting JSON data into Apache Arrow and Parquet formats.

## Key Functions

### `out_s3_compress_arrow`
Main entry point for Arrow/Feather compression.
- **Parameters**:
  - `json`: Pointer to JSON data to compress
  - `size`: Size of the JSON data in bytes
  - `out_buf`: Output parameter for the compressed data buffer
  - `out_size`: Output parameter for the size of compressed data
- **Returns**: 0 on success, -1 on failure
- **Description**: Converts JSON data to Arrow Table format and then to Feather format

### `out_s3_compress_parquet` (Conditional)
Main entry point for Parquet compression.
- **Parameters**:
  - `json`: Pointer to JSON data to compress
  - `size`: Size of the JSON data in bytes
  - `out_buf`: Output parameter for the compressed data buffer
  - `out_size`: Output parameter for the size of compressed data
- **Returns**: 0 on success, -1 on failure
- **Description**: Converts JSON data to Arrow Table format and then to Parquet format
- **Availability**: Only declared when `FLB_HAVE_ARROW_PARQUET` is defined

## Important Variables

- `FLB_HAVE_ARROW_PARQUET`: Compile-time flag controlling Parquet support availability

## Dependencies and Relationships

This header file:
- Declares functions implemented in `compress.c`
- Is included by the S3 output plugin and other components needing Arrow compression
- Depends on standard C types (void*, size_t)
- Integrates with the AWS compression system

## Notable Implementation Details

1. **Simple Interface**: Clean, straightforward function signatures
2. **Conditional Declarations**: Parquet function only declared when supported
3. **Memory Management**: Caller is responsible for freeing the returned buffer
4. **Error Reporting**: Simple integer return codes for success/failure
5. **Documentation**: Inline comments explain parameters and behavior

## Usage Examples

The functions are typically called by the S3 output plugin:
```c
#include "src/aws/compression/arrow/compress.h"

void *compressed_data;
size_t compressed_size;

// For Arrow/Feather compression
if (out_s3_compress_arrow(json_data, json_size, &compressed_data, &compressed_size) == 0) {
    // Use compressed_data
    // Remember to free compressed_data when done
}

// For Parquet compression (when available)
#ifdef FLB_HAVE_ARROW_PARQUET
if (out_s3_compress_parquet(json_data, json_size, &compressed_data, &compressed_size) == 0) {
    // Use compressed_data
    // Remember to free compressed_data when done
}
#endif
```