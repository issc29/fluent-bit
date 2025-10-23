# c-ares

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

## Examples

### Basic DNS Resolution Example
```c
#include <ares.h>
#include <stdio.h>
#include <stdlib.h>

void dns_callback(void *arg, int status, int timeouts, struct hostent *host)
{
    if (status != ARES_SUCCESS) {
        fprintf(stderr, "DNS lookup failed: %s\n", ares_strerror(status));
        return;
    }

    printf("Hostname: %s\n", host->h_name);
    printf("IP Address: %s\n", inet_ntoa(*(struct in_addr*)host->h_addr_list[0]));
}

int main()
{
    ares_channel channel;
    
    // Initialize the library
    if (ares_library_init(ARES_LIB_INIT_ALL) != ARES_SUCCESS) {
        fprintf(stderr, "Failed to initialize c-ares\n");
        return 1;
    }

    // Initialize a channel
    if (ares_init(&channel) != ARES_SUCCESS) {
        fprintf(stderr, "Failed to initialize channel\n");
        ares_library_cleanup();
        return 1;
    }

    // Perform a DNS lookup
    ares_gethostbyname(channel, "example.com", AF_INET, dns_callback, NULL);

    // Process events until the query completes
    int nfds;
    fd_set read_fds, write_fds;
    struct timeval tv, *tvp;
    
    while (1) {
        FD_ZERO(&read_fds);
        FD_ZERO(&write_fds);
        nfds = ares_fds(channel, &read_fds, &write_fds);
        if (nfds == 0)
            break;
            
        tvp = ares_timeout(channel, NULL, &tv);
        select(nfds, &read_fds, &write_fds, NULL, tvp);
        ares_process(channel, &read_fds, &write_fds);
    }

    // Cleanup
    ares_destroy(channel);
    ares_library_cleanup();
    
    return 0;
}
```