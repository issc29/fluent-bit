# src/aws/compression/arrow/CMakeLists.txt Documentation

## Overview

This CMakeLists.txt file is the build configuration for Arrow-based compression functionality in Fluent Bit's AWS integration. It defines the `flb-aws-arrow` static library target that provides Arrow compression capabilities.

## Key Components

### Source Files
- `compress.c`: Implementation of Arrow-based compression functionality

### Build Targets
- `flb-aws-arrow`: Static library containing Arrow compression functionality

## Important Variables

- `ARROW_GLIB_INCLUDE_DIRS`: Include directories for Arrow GLib bindings
- `ARROW_GLIB_LDFLAGS`: Link flags for Arrow GLib bindings
- `ARROW_GLIB_PARQUET_INCLUDE_DIRS`: Include directories for Arrow Parquet bindings (when available)
- `ARROW_GLIB_PARQUET_LDFLAGS`: Link flags for Arrow Parquet bindings (when available)
- `FLB_JEMALLOC`: Build flag to enable jemalloc memory allocator

## Dependencies and Relationships

This file is part of the Arrow compression module and:
- Creates a static library (`flb-aws-arrow`) with Arrow compression functionality
- Links to Arrow GLib bindings for core functionality
- Optionally links to Arrow Parquet bindings when available
- Integrates with the main AWS compression build system via `add_subdirectory(arrow)` in src/aws/compression/CMakeLists.txt
- Can be linked by the AWS compression interface library (`flb-aws-compress`)

## Notable Implementation Details

1. **Arrow Integration**: Uses Arrow GLib bindings for compression functionality
2. **Optional Parquet Support**: Conditionally includes Parquet bindings when available
3. **Memory Allocator Support**: Optionally links to jemalloc when enabled
4. **Modular Design**: Separates Arrow compression into its own library
5. **Proper Linking**: Correctly sets up include directories and link libraries

## Usage Examples

The Arrow compression library can be used by other targets:
```cmake
target_link_libraries(my_target flb-aws-arrow)
```

To enable Arrow compression support:
```bash
cmake -DFLB_ARROW=ON ..
make
```