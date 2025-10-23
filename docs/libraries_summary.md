# Libraries Documentation Summary

This document provides a quick reference guide for developers working with the key libraries used in this project.

## lib/avro

### Overview

Apache Avro is a data serialization system that provides rich data structures and a compact, fast, binary data format. It is designed for efficient serialization of data and includes support for schema evolution.

Avro provides:
- Rich data structures
- A compact, fast, binary data format
- A container file to store persistent data
- Remote procedure call (RPC)

Avro is particularly useful for:
- Data exchange between different systems
- Storing data in a compact binary format
- Schema evolution without breaking compatibility
- Efficient serialization/deserialization operations

### Key Methods/Functions

#### Schema Management
- `avro_schema_from_json_literal()` - Parse a schema from a JSON string literal
- `avro_schema_decref()` - Decrement the reference count of a schema
- `avro_schema_record()` - Create a record schema
- `avro_schema_record_field_append()` - Append a field to a record schema
- `avro_schema_string()` - Create a string schema

#### Value Operations
- `avro_generic_class_from_schema()` - Create a value class from a schema
- `avro_generic_value_new()` - Create a new generic value
- `avro_value_get_by_name()` - Get a field value by name
- `avro_value_set_long()` - Set a long value
- `avro_value_set_string()` - Set a string value
- `avro_value_set_int()` - Set an integer value
- `avro_value_get_long()` - Get a long value
- `avro_value_get_string()` - Get a string value
- `avro_value_get_int()` - Get an integer value
- `avro_value_decref()` - Decrement the reference count of a value
- `avro_value_iface_decref()` - Decrement the reference count of a value interface

#### File Operations
- `avro_file_writer_create_with_codec()` - Create a new file writer with compression
- `avro_file_writer_append_value()` - Append a value to the file
- `avro_file_writer_flush()` - Flush the file writer
- `avro_file_writer_close()` - Close the file writer
- `avro_file_reader()` - Open a file for reading
- `avro_file_reader_read_value()` - Read a value from the file
- `avro_file_reader_close()` - Close the file reader

#### Error Handling
- `avro_strerror()` - Get the error message for the last operation

### Important Usage Notes

1. **Schema Evolution**: Avro supports schema evolution, allowing you to add/remove fields while maintaining backward compatibility.
2. **Reference Counting**: Many Avro objects use reference counting. Always decrement references with decref functions to prevent memory leaks.
3. **Binary Format**: Avro uses a compact binary format that is efficient for both storage and transmission.
4. **Schema First**: Avro requires a schema to be defined before serializing data, which helps ensure data consistency.
5. **Value API**: Starting with version 1.6.0, Avro C library has a new API for handling Avro data using the value interface, which is more efficient than the legacy datum API. The value API provides:
   - Better performance for complex data structures
   - Support for custom value implementations
   - Generic value implementation that works for any Avro schema
   - Wrapper implementation for deprecated avro_datum_t type
6. **Memory Management**: Avro handles its own memory management, but users should properly destroy objects when no longer needed.
7. **Schema Validation**: Data written to an Avro File Object Container is always validated.
8. **Dependency**: Avro depends on the Jansson JSON parser (version 2.3 or higher) for schema parsing.

## lib/c-ares-1.34.4

### Overview

c-ares is a modern DNS (stub) resolver library written in C. It provides interfaces for asynchronous DNS queries while abstracting the intricacies of the underlying DNS protocol. Originally intended for applications that need to perform DNS queries without blocking or need to perform multiple DNS queries in parallel.

c-ares is designed to be a better DNS resolver than the system's default resolver, regardless of the operating system. It supports a wide range of DNS record types and RFCs, making it suitable for network applications that require robust DNS resolution capabilities.

Key features of c-ares include:
- Asynchronous DNS queries
- Support for multiple concurrent queries
- Cross-platform compatibility (Linux, Windows, macOS, etc.)
- Security-focused implementation with safe parsers
- Extensive RFC compliance
- MIT licensed for both free and commercial use
- Support for a wide range of DNS record types including A, AAAA, MX, SRV, TXT, and more
- Support for DNS-over-TCP and DNS-over-UDP
- Support for DNS cookies for off-path DNS poisoning and amplification mitigation
- Support for DNS 0x20 query name case randomization to prevent cache poisoning attacks
- Support for IPv6 address sorting as used by `ares_getaddrinfo()`
- Support for TCP FastOpen (TFO) for 0-RTT TCP Connection Resumption

### Key Methods/Functions

#### Library Initialization
- `ares_library_init()` - Initialize the c-ares library
- `ares_library_cleanup()` - Clean up the c-ares library resources
- `ares_library_initialized()` - Check if the library is initialized
- `ares_version()` - Get the c-ares version information

#### Channel Management
- `ares_init()` - Initialize a channel with default options
- `ares_init_options()` - Initialize a channel with custom options
- `ares_save_options()` - Save the current channel options
- `ares_destroy_options()` - Destroy channel options
- `ares_destroy()` - Destroy a channel
- `ares_reinit()` - Reinitialize a channel
- `ares_dup()` - Duplicate a channel

#### DNS Queries
- `ares_query()` - Perform a DNS query
- `ares_search()` - Perform a DNS search query
- `ares_gethostbyname()` - Resolve a hostname to an IP address
- `ares_gethostbyaddr()` - Resolve an IP address to a hostname
- `ares_getnameinfo()` - Convert a socket address to a hostname and service name
- `ares_getaddrinfo()` - Resolve a hostname to address information
- `ares_send()` - Send a raw DNS query

#### Socket Operations
- `ares_fds()` - Get the file descriptors for the channel
- `ares_getsock()` - Get socket information for the channel
- `ares_timeout()` - Get the timeout for the next operation
- `ares_process()` - Process events on the channel
- `ares_process_fd()` - Process events on specific file descriptors
- `ares_process_fds()` - Process events on multiple file descriptors

#### Configuration
- `ares_set_local_ip4()` - Set the local IPv4 address for queries
- `ares_set_local_ip6()` - Set the local IPv6 address for queries
- `ares_set_local_dev()` - Set the local network device for queries
- `ares_set_socket_callback()` - Set a callback for socket creation
- `ares_set_socket_configure_callback()` - Set a callback for socket configuration
- `ares_set_sortlist()` - Set the sort list for addresses
- `ares_set_server_state_callback()` - Set a callback for server state changes

#### Utility Functions
- `ares_expand_name()` - Expand a compressed DNS name
- `ares_expand_string()` - Expand a compressed string
- `ares_create_query()` - Create a DNS query
- `ares_mkquery()` - Create a DNS query (legacy)

### Important Usage Notes

1. **Asynchronous Nature**: c-ares is designed for asynchronous operations. Queries don't block and results are delivered via callbacks.
2. **Channel Management**: Always initialize the library with `ares_library_init()` before creating channels, and clean up with `ares_library_cleanup()` when done.
3. **Event Processing**: Use `ares_process()` or related functions to process events and trigger callbacks.
4. **Memory Management**: The library manages its own memory, but applications should ensure proper cleanup of channels and options.
5. **Thread Safety**: c-ares is not thread-safe by default. Multiple threads should use separate channels or implement their own synchronization.
6. **RFC Compliance**: c-ares has extensive RFC compliance, supporting:
   - RFC1035: Initial/Base DNS RFC
   - RFC2671/RFC6891: EDNS0 option (meta-RR)
   - RFC3596: IPv6 Address. `AAAA` Record.
   - RFC2782: Server Selection. `SRV` Record.
   - RFC3403: Naming Authority Pointer. `NAPTR` Record.
   - RFC6698: DNS-Based Authentication of Named Entities (DANE) Transport Layer Security (TLS) Protocol. `TLSA` Record.
   - RFC9460: General Purpose Service Binding, Service Binding type for use with HTTPS. `SVCB` and `HTTPS` Records.
   - RFC7553: Uniform Resource Identifier. `URI` Record.
   - RFC6844: Certification Authority Authorization. `CAA` Record.
   - RFC2535/RFC2931: `SIG0` Record. Only basic parser, not full implementation.
   - RFC7873/RFC9018: DNS Cookie off-path dns poisoning and amplification mitigation.
   - draft-vixie-dnsext-dns0x20-00: DNS 0x20 query name case randomization to prevent cache poisoning attacks.
   - RFC7686: Reject queries for `.onion` domain names with `NXDOMAIN`.
   - RFC2606/RFC6761: Special case treatment for `localhost`/`.localhost`.
   - RFC2308/RFC9520: Negative Caching of DNS Resolution Failures.
   - RFC6724: IPv6 address sorting as used by `ares_getaddrinfo()`.
   - RFC7413: TCP FastOpen (TFO) for 0-RTT TCP Connection Resumption.
   - RFC3986: Uniform Resource Identifier (URI). Used for server configuration.
   - RFC7091: DNS-Based Authentication of Named Entities (DANE) TLS Protocol: TLSA Record.
   - RFC7626: DNS Privacy Considerations.
   - RFC8499: DNS Terminology.
7. **Security Features**: c-ares includes security-focused implementation with safe parsers and is constantly validated with a range of static and dynamic analyzers, as well as being constantly fuzzed by OSS Fuzz.
8. **Cross-platform Compatibility**: c-ares runs on Linux, FreeBSD, OpenBSD, MacOS, Solaris, AIX, Windows, Android, iOS and many more operating systems.
9. **License**: c-ares is MIT licensed, suitable for both free and commercial software.

## lib/cfl

### Overview

CFL is a tiny library that provides interfaces for data structures, originally created to satisfy the needs of Fluent Bit and other libraries used internally like CMetrics and CTraces projects. It offers a collection of lightweight, efficient data structures and utilities commonly needed in C applications.

CFL provides implementations for:
- String manipulation (Simple Dynamic Strings)
- Linked lists
- Key-value pairs
- Arrays
- Variant types
- Time utilities
- Hash functions

The library is designed to be minimal and focused, providing only what's needed for efficient data handling in performance-critical applications.

Note: The name doesn't mean anything specific, you can call it `c:\ floppy` if you want.

License: This program is under the terms of the [Apache License v2.0](http://www.apache.org/licenses/LICENSE-2.0).

### Key Methods/Functions

#### Core Library
- `cfl_init()` - Initialize the CFL library
- `cfl_version()` - Get the CFL version string

#### String Operations (cfl_sds)
- `cfl_sds_create()` - Create a new SDS string
- `cfl_sds_create_len()` - Create a new SDS string with specified length
- `cfl_sds_create_size()` - Create a new SDS string with specified size
- `cfl_sds_destroy()` - Destroy an SDS string
- `cfl_sds_cat()` - Concatenate a string to an SDS
- `cfl_sds_cat_safe()` - Safely concatenate a string to an SDS
- `cfl_sds_printf()` - Format and append to an SDS
- `cfl_sds_len()` - Get the length of an SDS
- `cfl_sds_alloc()` - Get the allocated size of an SDS
- `cfl_sds_avail()` - Get the available space in an SDS
- `cfl_sds_increase()` - Increase the size of an SDS
- `cfl_sds_set_len()` - Set the length of an SDS

#### Linked List Operations (cfl_list)
- `cfl_list_init()` - Initialize a list
- `cfl_list_add()` - Add an item to the beginning of a list
- `cfl_list_append()` - Add an item to the end of a list
- `cfl_list_prepend()` - Add an item to the beginning of a list
- `cfl_list_del()` - Remove an item from a list
- `cfl_list_is_empty()` - Check if a list is empty
- `cfl_list_size()` - Get the size of a list
- `cfl_list_entry_init()` - Initialize a list entry
- `cfl_list_entry_is_orphan()` - Check if a list entry is orphaned
- `cfl_list_cat()` - Concatenate two lists

#### Key-Value Operations (cfl_kv)
- `cfl_kv_create()` - Create a new key-value pair
- `cfl_kv_destroy()` - Destroy a key-value pair
- `cfl_kv_item_create()` - Create a new key-value item
- `cfl_kv_item_destroy()` - Destroy a key-value item

#### Array Operations (cfl_array)
- `cfl_array_create()` - Create a new array
- `cfl_array_destroy()` - Destroy an array
- `cfl_array_append()` - Append an element to an array
- `cfl_array_get()` - Get an element from an array
- `cfl_array_size()` - Get the size of an array
- `cfl_array_is_empty()` - Check if an array is empty

#### Hash Operations (cfl_hash)
- `cfl_hash_64bits()` - Compute a 64-bit hash
- `cfl_hash_64bits_reset()` - Reset a hash state
- `cfl_hash_64bits_update()` - Update a hash state
- `cfl_hash_64bits_digest()` - Get the digest of a hash state
- `cfl_hash_128bits()` - Compute a 128-bit hash

#### Time Operations (cfl_time)
- `cfl_time_now()` - Get the current time in nanoseconds

#### Variant Operations (cfl_variant)
- `cfl_variant_create()` - Create a new variant
- `cfl_variant_destroy()` - Destroy a variant
- `cfl_variant_set_int64()` - Set a variant to an int64 value
- `cfl_variant_set_uint64()` - Set a variant to a uint64 value
- `cfl_variant_set_double()` - Set a variant to a double value
- `cfl_variant_set_bool()` - Set a variant to a boolean value
- `cfl_variant_set_string()` - Set a variant to a string value
- `cfl_variant_get_int64()` - Get an int64 value from a variant
- `cfl_variant_get_uint64()` - Get a uint64 value from a variant
- `cfl_variant_get_double()` - Get a double value from a variant
- `cfl_variant_get_bool()` - Get a boolean value from a variant
- `cfl_variant_get_string()` - Get a string value from a variant

### Important Usage Notes

1. **Minimal Design**: CFL is intentionally minimal, focusing only on essential data structures and utilities.
2. **Performance Focus**: All implementations are designed for efficiency in performance-critical applications.
3. **Memory Management**: CFL handles its own memory management, but users should properly destroy objects when no longer needed.
4. **String Safety**: The SDS (Simple Dynamic String) implementation is designed to be safe and efficient for string operations.
5. **List Efficiency**: The linked list implementation is highly optimized for common operations like insertion and deletion.
6. **Hash Quality**: The hash functions use high-quality algorithms (XXH3) for good distribution properties. CFL uses the xxHash library which provides:
   - XXH32: generates 32-bit hashes, using 32-bit arithmetic
   - XXH64: generates 64-bit hashes, using 64-bit arithmetic
   - XXH3: generates 64 or 128-bit hashes, using vectorized arithmetic
   All variants successfully complete the SMHasher test suite which evaluates the quality of hash functions.
7. **Thread Safety**: CFL is designed to be thread-safe for most operations, making it suitable for concurrent applications.