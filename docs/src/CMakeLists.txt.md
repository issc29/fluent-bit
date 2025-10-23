# src/CMakeLists.txt Documentation

## Overview

This CMakeLists.txt file is the main build configuration for the Fluent Bit core source code. It defines the build targets, source files, dependencies, and compilation options for the Fluent Bit project.

## Key Components

### Source Files
The file defines the core source files that make up Fluent Bit:
- Core API and utility functions (`flb_api.c`, `flb_lib.c`, `flb_log.c`)
- Input/output/filter processing components
- Configuration management (`flb_config.c`, `flb_config_map.c`)
- Network and HTTP client functionality
- Compression and serialization libraries
- Plugin management system

### Conditional Compilation
The file uses conditional compilation blocks to include/exclude features based on build flags:
- `FLB_PARSER`: Parser functionality
- `FLB_AVRO_ENCODER`: Avro encoding support
- `FLB_TLS`: TLS/SSL support
- `FLB_PROXY_GO`: Go proxy support
- `FLB_METRICS`: Metrics collection
- `FLB_SIGNV4`: AWS Signature V4 support
- `FLB_HTTP_CLIENT_DEBUG`: HTTP client debugging
- `FLB_AWS_ERROR_REPORTER`: AWS error reporting
- `FLB_LUAJIT`: LuaJIT scripting support
- `FLB_KAFKA`: Kafka output support

### Dependencies
The file manages various dependencies:
- System libraries (pthread, dl, m)
- Third-party libraries (OpenSSL, libyaml, jansson)
- Internal libraries (co, rbtree, chunkio)
- Platform-specific libraries (Windows ws2_32, crypt32)

### Build Targets
Defines three main build targets:
1. `fluent-bit-shared`: Shared library version
2. `fluent-bit-static`: Static library version
3. `fluent-bit-bin`: Executable binary

## Important Variables

- `src`: List of source files to compile
- `FLB_DEPS`: List of dependencies to link
- `extra_libs`: Additional libraries
- Various feature flags (FLB_PARSER, FLB_TLS, etc.)

## Dependencies and Relationships

This file is the central build configuration that integrates all components of Fluent Bit:
- Links to sub-components via `add_subdirectory()` calls
- Depends on external libraries managed by the main CMakeLists.txt
- Integrates platform-specific code through conditional compilation
- Connects to plugin architecture through FLB_PLUGINS

## Notable Implementation Details

1. **Modular Architecture**: Uses conditional compilation to enable/disable features
2. **Cross-platform Support**: Handles Windows, Linux, and macOS differences
3. **Dependency Management**: Centralized handling of all external dependencies
4. **Build Variants**: Supports shared, static, and executable builds
5. **Installation Routines**: Defines how the built artifacts are installed

## Usage Examples

To build Fluent Bit with specific features:
```bash
cmake -DFLB_TLS=ON -DFLB_PARSER=ON ..
make
```

To build only the static library:
```bash
cmake -DFLB_BINARY=OFF ..
make fluent-bit-static
```