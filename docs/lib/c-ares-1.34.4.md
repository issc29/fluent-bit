# lib/c-ares-1.34.4

## Overview

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

## Key Methods/Functions

### Library Initialization
- `ares_library_init()` - Initialize the c-ares library
- `ares_library_cleanup()` - Clean up the c-ares library resources
- `ares_library_initialized()` - Check if the library is initialized
- `ares_version()` - Get the c-ares version information

### Channel Management
- `ares_init()` - Initialize a channel with default options
- `ares_init_options()` - Initialize a channel with custom options
- `ares_save_options()` - Save the current channel options
- `ares_destroy_options()` - Destroy channel options
- `ares_destroy()` - Destroy a channel
- `ares_reinit()` - Reinitialize a channel
- `ares_dup()` - Duplicate a channel

### DNS Queries
- `ares_query()` - Perform a DNS query
- `ares_search()` - Perform a DNS search query
- `ares_gethostbyname()` - Resolve a hostname to an IP address
- `ares_gethostbyaddr()` - Resolve an IP address to a hostname
- `ares_getnameinfo()` - Convert a socket address to a hostname and service name
- `ares_getaddrinfo()` - Resolve a hostname to address information
- `ares_send()` - Send a raw DNS query

### Socket Operations
- `ares_fds()` - Get the file descriptors for the channel
- `ares_getsock()` - Get socket information for the channel
- `ares_timeout()` - Get the timeout for the next operation
- `ares_process()` - Process events on the channel
- `ares_process_fd()` - Process events on specific file descriptors
- `ares_process_fds()` - Process events on multiple file descriptors

### Configuration
- `ares_set_local_ip4()` - Set the local IPv4 address for queries
- `ares_set_local_ip6()` - Set the local IPv6 address for queries
- `ares_set_local_dev()` - Set the local network device for queries
- `ares_set_socket_callback()` - Set a callback for socket creation
- `ares_set_socket_configure_callback()` - Set a callback for socket configuration
- `ares_set_sortlist()` - Set the sort list for addresses
- `ares_set_server_state_callback()` - Set a callback for server state changes

### Utility Functions
- `ares_expand_name()` - Expand a compressed DNS name
- `ares_expand_string()` - Expand a compressed string
- `ares_create_query()` - Create a DNS query
- `ares_mkquery()` - Create a DNS query (legacy)

## Important Usage Notes

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
7. **Security Features**: c-ares includes security-focused implementation with safe parsers and is constantly validated with a range of static and dynamic analyzers, as well as being constantly fuzzed by OSS Fuzz.
8. **Cross-platform Compatibility**: c-ares runs on Linux, FreeBSD, OpenBSD, MacOS, Solaris, AIX, Windows, Android, iOS and many more operating systems.
9. **License**: c-ares is MIT licensed, suitable for both free and commercial software.