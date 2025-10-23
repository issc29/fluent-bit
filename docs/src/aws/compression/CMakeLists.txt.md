# src/aws/compression/CMakeLists.txt Documentation

## Overview

This CMakeLists.txt file is the build configuration for AWS compression functionality in Fluent Bit. It defines the `flb-aws-compress` interface library and conditionally includes Arrow-based compression support.

## Key Components

### Build Targets
- `flb-aws-compress`: An INTERFACE library that serves as a placeholder for AWS compression functionality
- Conditionally includes the `arrow` subdirectory when Arrow compression is enabled

### Conditional Compilation
The file uses conditional compilation to include Arrow compression support:
- `FLB_ARROW`: Enables Arrow-based compression functionality
- `FLB_HAVE_ARROW_PARQUET`: Enables Parquet compression support (when Arrow is enabled)

## Important Variables

- `FLB_ARROW`: Build flag to enable Arrow compression support
- `FLB_HAVE_ARROW_PARQUET`: Build flag to enable Parquet compression support

## Dependencies and Relationships

This file is part of the AWS compression module and:
- Creates an INTERFACE library that can be linked by other targets
- Conditionally includes Arrow compression via `add_subdirectory(arrow)`
- Links to the Arrow compression library (`flb-aws-arrow`) when enabled
- Integrates with the main AWS build system via `add_subdirectory(compression)` in src/aws/CMakeLists.txt

## Notable Implementation Details

1. **Interface Library**: Uses INTERFACE library type to provide compile/link options without creating object files
2. **Conditional Inclusion**: Only includes Arrow compression when explicitly enabled
3. **Modular Design**: Separates compression functionality into its own module
4. **Dependency Management**: Properly links to Arrow compression library when enabled
5. **Parquet Support**: Conditionally supports Parquet compression when Arrow is enabled

## Usage Examples

To build with Arrow compression support:
```bash
cmake -DFLB_ARROW=ON ..
make
```

To build with Parquet compression support:
```bash
cmake -DFLB_ARROW=ON -DFLB_HAVE_ARROW_PARQUET=ON ..
make
```

The interface library can be used by other targets:
```cmake
target_link_libraries(my_target flb-aws-compress)
```