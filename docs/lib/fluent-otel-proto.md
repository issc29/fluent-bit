# lib/fluent-otel-proto

## Overview

Fluent OTel Proto is a library that builds a static library providing C interfaces for OpenTelemetry proto files data model and includes helper utilities. It serves as a bridge between Fluent Bit and the OpenTelemetry ecosystem by exposing C interfaces for OpenTelemetry protocol buffers.

The library includes pre-generated C interfaces from OpenTelemetry proto files, eliminating the need for developers to manage complex protobuf compilation processes. It supports multiple OpenTelemetry data models including Common, Resource, Trace, Logs, and Metrics (when enabled).

The project is designed to be lightweight and focused, exposing only the necessary interfaces while maintaining full compatibility with OpenTelemetry specifications.

## Key Methods/Functions

### Core Library
- `fluent_otel_info()` - Display information about the Fluent OTel library and its capabilities

### Protocol Buffer Interfaces
The library exposes C interfaces for the following OpenTelemetry proto files:

**Common Proto**
- `opentelemetry/proto/common/v1/common.pb-c.h` - Common data structures and utilities

**Resource Proto**
- `opentelemetry/proto/resource/v1/resource.pb-c.h` - Resource data model interfaces

**Trace Proto**
- `opentelemetry/proto/trace/v1/trace.pb-c.h` - Trace data model interfaces
- `opentelemetry/proto/collector/trace/v1/trace_service.pb-c.h` - Trace collector service interfaces

**Logs Proto**
- `opentelemetry/proto/logs/v1/logs.pb-c.h` - Logs data model interfaces
- `opentelemetry/proto/collector/logs/v1/logs_service.pb-c.h` - Logs collector service interfaces

**Metrics Proto**
- `opentelemetry/proto/metrics/v1/metrics.pb-c.h` - Metrics data model interfaces (disabled by default)
- `opentelemetry/proto/collector/metrics/v1/metrics_service.pb-c.h` - Metrics collector service interfaces (disabled by default)

## Important Usage Notes

1. **Pre-generated Interfaces**: The library comes with pre-generated C interfaces from OpenTelemetry proto files, eliminating the need for protobuf compilation in most cases.
2. **Feature Toggles**: Different OpenTelemetry data models can be enabled/disabled at build time using CMake options:
   - `FLUENT_PROTO_COMMON` - Enable Common proto interfaces (default: ON)
   - `FLUENT_PROTO_RESOURCE` - Enable Resource proto interfaces (default: ON)
   - `FLUENT_PROTO_TRACE` - Enable Trace proto interfaces (default: ON)
   - `FLUENT_PROTO_LOGS` - Enable Logs proto interfaces (default: ON)
   - `FLUENT_PROTO_METRICS` - Enable Metrics proto interfaces (default: OFF)
3. **Build Dependencies**: To regenerate C files from proto definitions, the library requires:
   - `fluent/protobuf-c` - Fork of protobuf-c with proto3 options support
   - `open-telemetry/opentelemetry-proto` - Official OpenTelemetry proto definitions
4. **Regeneration Process**: C files can be regenerated using CMake with specific variables:
   - `FLUENT_PROTO_REGENERATE` - Enable regeneration (default: OFF)
   - `PROTOBUF_C_SOURCE_DIR` - Path to protobuf-c source directory
   - `OTEL_PROTO_DIR` - Path to opentelemetry-proto source directory
5. **Lightweight Design**: The library is designed to be minimal, exposing only necessary interfaces without bloating the application.
6. **Compatibility**: Fully compatible with OpenTelemetry specifications and protocols.
7. **Static Library**: Built as a static library for easy integration into Fluent Bit and other C applications.
8. **Memory Management**: Follows standard protobuf-c memory management patterns.
9. **Thread Safety**: Designed to be thread-safe for concurrent usage scenarios.
10. **Metrics Disabled**: Metrics support is disabled by default due to complexity and resource requirements.
11. **Version Alignment**: Maintains alignment with OpenTelemetry proto specification versions.
12. **Helper Utilities**: Includes utility functions to simplify common operations with OpenTelemetry data models.