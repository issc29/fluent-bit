# src/CMakeLists.txt Documentation

## Overview

This CMakeLists.txt file is the main build configuration for the Fluent Bit core source code. It defines the core source files, dependencies, and build targets for the Fluent Bit project.

## Key Components

### Core Source Files
The file defines a comprehensive list of core source files that make up the Fluent Bit engine:
- `flb_mp.c` - MessagePack implementation
- `flb_kv.c` - Key-value utilities
- `flb_api.c` - Core API functions
- `flb_csv.c` - CSV parsing utilities
- `flb_lib.c` - Library initialization and main functions
- `flb_log.c` - Logging utilities
- `flb_env.c` - Environment variable handling
- `flb_file.c` - File operations
- `flb_uri.c` - URI parsing
- `flb_utf8.c` - UTF-8 handling
- `flb_hash_table.c` - Hash table implementation
- `flb_help.c` - Help system
- `flb_pack.c` - Data packing utilities
- `flb_pack_json.c` - JSON packing
- `flb_pack_gelf.c` - GELF packing
- `flb_sds.c` - Simple Dynamic Strings implementation
- `flb_sds_list.c` - SDS list operations
- `flb_pipe.c` - Pipe operations
- `flb_meta.c` - Metadata handling
- `flb_kernel.c` - Kernel utilities
- `flb_custom.c` - Custom plugin support
- `flb_input.c` - Input plugin framework
- `flb_input_chunk.c` - Chunk-based input
- `flb_input_log.c` - Log input processing
- `flb_input_metric.c` - Metric input processing
- `flb_input_trace.c` - Trace input processing
- `flb_input_profiles.c` - Input profiles
- `flb_input_blob.c` - Blob input processing
- `flb_input_thread.c` - Threaded input processing
- `flb_filter.c` - Filter plugin framework
- `flb_output.c` - Output plugin framework
- `flb_output_thread.c` - Threaded output processing
- `flb_config.c` - Configuration management
- `flb_config_map.c` - Configuration mapping
- `flb_socket.c` - Socket operations
- `flb_network.c` - Network utilities
- `flb_utils.c` - General utilities
- `flb_slist.c` - String list operations
- `flb_engine.c` - Main engine
- `flb_engine_dispatch.c` - Engine dispatch
- `flb_task.c` - Task management
- `flb_unescape.c` - String unescaping
- `flb_scheduler.c` - Task scheduling
- `flb_io.c` - I/O operations
- `flb_storage.c` - Storage management
- `flb_connection.c` - Connection management
- `flb_downstream.c` - Downstream connections
- `flb_upstream.c` - Upstream connections
- `flb_upstream_ha.c` - High availability upstream
- `flb_upstream_node.c` - Upstream node management
- `flb_router.c` - Routing engine
- `flb_router_condition.c` - Routing conditions
- `flb_router_config.c` - Routing configuration
- `flb_worker.c` - Worker threads
- `flb_coro.c` - Coroutine support
- `flb_time.c` - Time utilities
- `flb_sosreport.c` - SOS report generation
- `flb_hmac.c` - HMAC implementation
- `flb_hash.c` - Hash functions
- `flb_crypto.c` - Cryptographic utilities
- `flb_random.c` - Random number generation
- `flb_plugin.c` - Plugin framework
- `flb_gzip.c` - GZIP compression
- `flb_snappy.c` - Snappy compression
- `flb_zstd.c` - Zstandard compression
- `flb_compression.c` - Compression framework
- `flb_http_common.c` - HTTP common utilities
- `flb_http_client_http1.c` - HTTP/1.1 client
- `flb_http_client_http2.c` - HTTP/2 client
- `flb_http_client.c` - HTTP client framework
- `flb_callback.c` - Callback system
- `flb_strptime.c` - Date/time parsing
- `flb_fstore.c` - File storage
- `flb_thread_pool.c` - Thread pool
- `flb_routes_mask.c` - Route masking
- `flb_typecast.c` - Type casting
- `flb_event.c` - Event system
- `flb_base64.c` - Base64 encoding
- `flb_ring_buffer.c` - Ring buffer implementation
- `flb_log_event_decoder.c` - Log event decoding
- `flb_log_event_encoder.c` - Log event encoding
- `flb_log_event_encoder_primitives.c` - Primitive encoders
- `flb_log_event_encoder_dynamic_field.c` - Dynamic field encoders
- `flb_processor.c` - Data processor
- `flb_reload.c` - Configuration reloading
- `flb_supervisor.c` - Supervisor process
- `flb_msgpack_append_message.c` - MessagePack message appending
- `flb_notification.c` - Notification system
- `flb_lock.c` - Locking mechanisms
- `flb_cfl_ra_key.c` - CFL record accessor key
- `flb_cfl_record_accessor.c` - CFL record accessor
- `flb_conditionals.c` - Conditional expressions
- `flb_mem.c` - Memory management
- `flb_blob_db.c` - Blob database

### Configuration Format Support
- `config_format/flb_config_format.c` - Configuration format framework
- `config_format/flb_cf_fluentbit.c` - Fluent Bit configuration format
- `config_format/flb_cf_yaml.c` - YAML configuration format (when FLB_HAVE_LIBYAML is enabled)

### OpenTelemetry Helpers
- `opentelemetry/flb_opentelemetry_logs.c` - OpenTelemetry logs
- `opentelemetry/flb_opentelemetry_traces.c` - OpenTelemetry traces
- `opentelemetry/flb_opentelemetry_utils.c` - OpenTelemetry utilities

### Multiline Subsystem
- Includes the multiline subdirectory

### Parser Support
- `flb_parser.c` - Parser framework
- `flb_parser_regex.c` - Regex parser
- `flb_parser_json.c` - JSON parser
- `flb_parser_decoder.c` - Parser decoder
- `flb_parser_ltsv.c` - LTSV parser
- `flb_parser_logfmt.c` - Logfmt parser

### AVRO Encoder Support
- `flb_avro.c` - AVRO encoding (when FLB_AVRO_ENCODER is enabled)

### TLS Support
- `flb_tls.c` - TLS implementation (when FLB_TLS is enabled)
- `flb_oauth2.c` - OAuth2 support

### Proxy Support
- `flb_plugin_proxy.c` - Plugin proxy (when FLB_PROXY_GO is enabled)

### Metrics Support
- `flb_metrics.c` - Metrics collection
- `flb_metrics_exporter.c` - Metrics exporter

### AWS Signature Support
- `flb_signv4.c` - AWS Signature V4 (when FLB_SIGNV4 and FLB_TLS are enabled)
- `flb_signv4_ng.c` - Next-generation AWS Signature V4

### HTTP Client Debug Support
- `flb_http_client_debug.c` - HTTP client debugging (when FLB_HTTP_CLIENT_DEBUG is enabled)

### AWS Error Reporter
- `aws/flb_aws_error_reporter.c` - AWS error reporting (when FLB_AWS_ERROR_REPORTER is enabled)

### Lua Support
- `flb_lua.c` - Lua scripting
- `flb_luajit.c` - LuaJIT support (when FLB_LUAJIT is enabled)

### Kafka Support
- `flb_kafka.c` - Kafka integration (when FLB_KAFKA is enabled)

### Dependencies
- Links to various external libraries including libco, rbtree, jemalloc (when enabled), onigmo (regex), sqlite (when enabled), static configuration support, chunk tracing, unicode support, SIMDUTF, and more.

### Build Targets
- Creates both shared and static libraries
- Builds the main fluent-bit executable
- Handles installation of binaries, libraries, and configuration files
- Manages systemd, upstart, and init.d service files
- Installs default configuration files

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