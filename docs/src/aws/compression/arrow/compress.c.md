# src/aws/compression/arrow/compress.c Documentation

## Overview

This C source file implements Arrow-based compression functionality for Fluent Bit's AWS integration. It provides functions to convert JSON data into Apache Arrow format (both Feather and Parquet formats) for efficient storage and transmission to AWS services.

## Key Functions

### `parse_json`
Converts raw JSON data into an Arrow Table structure using the Arrow GLib bindings.
- Takes JSON data and size as input
- Returns a GArrowTable object representing the parsed data
- Handles error conditions and resource cleanup
- Uses Arrow's JSON reader functionality

### `table_to_buffer`
Converts an Arrow Table to a Feather-formatted buffer.
- Takes a GArrowTable as input
- Returns a GArrowResizableBuffer containing the Feather-formatted data
- Uses Arrow's built-in Feather writer functionality
- Handles error conditions and resource cleanup

### `out_s3_compress_arrow`
Main entry point for Arrow compression.
- Converts JSON data to Arrow Table format
- Converts the table to Feather format
- Returns the compressed data buffer and size
- Used by the S3 output plugin for Arrow compression
- Handles all resource allocation and cleanup

### `table_to_parquet_buffer` (Conditional)
Converts an Arrow Table to a Parquet-formatted buffer.
- Only compiled when `FLB_HAVE_ARROW_PARQUET` is defined
- Takes a GArrowTable as input
- Returns a GArrowResizableBuffer containing the Parquet-formatted data
- Uses Arrow's Parquet writer functionality
- Handles error conditions and resource cleanup

### `out_s3_compress_parquet` (Conditional)
Main entry point for Parquet compression.
- Only compiled when `FLB_HAVE_ARROW_PARQUET` is defined
- Converts JSON data to Arrow Table format
- Converts the table to Parquet format
- Returns the compressed data buffer and size
- Used by the S3 output plugin for Parquet compression
- Handles all resource allocation and cleanup

## Important Variables

- `FLB_HAVE_ARROW_PARQUET`: Compile-time flag to enable Parquet support
- Various Arrow GLib objects (GArrowTable, GArrowBuffer, etc.)
- Error handling variables (GError pointers)
- Memory management variables (buffer pointers, sizes)

## Dependencies and Relationships

This file depends on:
- Arrow GLib bindings for core Arrow functionality
- Optional Parquet GLib bindings when Parquet support is enabled
- Fluent Bit core utilities (flb_log.h, flb_mem.h)
- Standard C libraries (inttypes.h)
- JSON parsing utilities

It is used by:
- The S3 output plugin for Arrow/Parquet compression
- The AWS compression interface library
- Other AWS plugins that require Arrow compression

## Notable Implementation Details

1. **GLib Binding Usage**: Uses Arrow GLib bindings to access Arrow C++ functionality from C
2. **Resource Management**: Careful memory management with proper reference counting
3. **Error Handling**: Comprehensive error checking and cleanup on failure paths
4. **Conditional Compilation**: Parquet support is optional and only compiled when available
5. **Format Support**: Supports both Feather (default) and Parquet formats
6. **Integration Point**: Designed to be called by S3 output plugin for data compression
7. **Memory Safety**: Proper allocation and deallocation of all resources
8. **Performance Optimization**: Efficient conversion from JSON to Arrow formats

## Usage Examples

The functions are typically called by the S3 output plugin:
```c
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

## Error Handling

The functions return -1 on failure and 0 on success. All error paths ensure proper cleanup of allocated resources to prevent memory leaks.