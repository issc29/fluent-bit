# src/aws/flb_aws_credentials_ec2.c Documentation

## Overview

This C source file implements the EC2 Instance Metadata Service (IMDS) credential provider for Fluent Bit's AWS integration. It provides functionality to retrieve temporary AWS credentials from EC2 instances using the Instance Metadata Service.

## Key Components

### EC2 Provider Structure
```c
struct flb_aws_provider_ec2 {
    struct flb_aws_credentials *creds;
    time_t next_refresh;
    struct flb_aws_client *client;
    struct flb_aws_imds *imds_interface;
};
```
- Stores cached credentials and refresh timing
- Manages HTTP client for IMDS communication
- Interfaces with IMDS-specific functionality
- Tracks next refresh time for automatic credential renewal

### Provider Functions

### `flb_ec2_provider_create`
Creates and initializes an EC2 credential provider.
- Sets up HTTP client for IMDS communication
- Configures IMDS interface with default settings
- Initializes mutex for thread safety
- Returns provider ready for credential retrieval
- Handles error conditions and resource cleanup

### `get_credentials_fn_ec2`
Retrieves AWS credentials from EC2 IMDS.
- Checks if cached credentials are expired or missing
- Uses mutex to prevent race conditions during refresh
- Returns deep copy of credentials for safe usage
- Handles concurrent access scenarios
- Implements refresh logic with proper locking

### `refresh_fn_ec2`
Forces refresh of EC2 credentials.
- Acquires mutex and calls internal refresh function
- Updates cached credentials and refresh timing
- Handles error conditions during refresh
- Returns appropriate error codes

### `init_fn_ec2`
Initializes EC2 provider with credentials.
- Performs initial credential fetch
- Sets up proper timeouts for IMDS communication
- Handles debug mode for initial fetch
- Returns success/failure status

### `sync_fn_ec2` / `async_fn_ec2`
Controls synchronous/asynchronous operation mode.
- Enables/disables async mode for HTTP client
- Used for coroutine compatibility
- Manages stream mode settings

### `upstream_set_fn_ec2`
Configures upstream connection for EC2 provider.
- Sets up HTTP client with proper timeouts
- Ensures IMDS-specific network configuration
- Handles TLS settings for secure communication
- Restores IMDS-specific timeout values

### `destroy_fn_ec2`
Cleans up EC2 provider resources.
- Destroys cached credentials
- Frees IMDS interface and HTTP client
- Releases mutex and provider structure
- Handles managed dependencies cleanup

### `get_creds_ec2`
Internal function to fetch credentials from IMDS.
- Retrieves instance role name from IMDS
- Constructs credential path for the role
- Requests credentials from IMDS endpoint
- Parses response and updates cached credentials
- Calculates next refresh time

### `ec2_credentials_request`
Handles the actual IMDS credential request.
- Makes HTTP request to IMDS endpoint
- Parses JSON response for credentials
- Extracts expiration time for refresh scheduling
- Handles error conditions and resource cleanup

## Important Variables and Constants

### IMDS Paths
- `AWS_IMDS_ROLE_PATH`: "/latest/meta-data/iam/security-credentials/" - Base path for role information
- `AWS_IMDS_ROLE_PATH_LEN`: 43 - Length of the base path

### Timing Constants
- `FLB_AWS_IMDS_TIMEOUT`: Timeout for IMDS requests
- `FLB_AWS_REFRESH_WINDOW`: Time window before expiration to refresh credentials

### Host Constants
- `FLB_AWS_IMDS_HOST`: "169.254.169.254" - IMDS endpoint IP address
- `FLB_AWS_IMDS_PORT`: 80 - IMDS endpoint port

## Dependencies and Relationships

This file depends on:
- Fluent Bit core utilities (flb_sds.h, flb_http_client.h, flb_aws_credentials.h)
- AWS utility functions (flb_aws_util.h)
- JSON parsing (flb_jsmn.h)
- IMDS interface (flb_aws_imds.h)
- Standard C libraries (stdlib.h, time.h, sys/types.h, sys/stat.h)
- Threading library (pthread.h)
- HTTP client utilities

It integrates with:
- Standard credential provider chain
- IMDS interface for metadata communication
- HTTP client for secure communication
- Output plugins requiring EC2 credentials
- TLS subsystem for secure connections

## Notable Implementation Details

1. **IMDS Protocol Support**: Handles both IMDSv1 and IMDSv2 protocols
2. **Thread Safety**: Uses mutex locks to prevent race conditions
3. **Credential Caching**: Caches credentials with automatic refresh timing
4. **Network Configuration**: Properly configures timeouts for IMDS communication
5. **Resource Management**: Comprehensive cleanup of all allocated resources
6. **Error Handling**: Robust error checking with appropriate logging
7. **Coroutine Compatibility**: Sync/async mode switching for coroutine support
8. **Security**: Uses proper HTTP client configuration for secure communication
9. **Memory Safety**: Proper allocation and deallocation with error handling
10. **Timeout Management**: Specific timeout settings for IMDS communication
11. **Path Construction**: Dynamic construction of IMDS paths for role credentials
12. **Response Parsing**: JSON parsing of IMDS credential responses

## Usage Examples

The functions are typically called by the standard credential provider chain:
```c
#include <fluent-bit/flb_aws_credentials.h>

// Create EC2 provider (typically done by standard chain)
struct flb_aws_provider *provider = flb_ec2_provider_create(config, generator);

// Retrieve credentials (called by standard chain)
struct flb_aws_credentials *creds = provider->provider_vtable->get_credentials(provider);

// Use credentials for AWS API calls
if (creds) {
    // Use creds->access_key_id, creds->secret_access_key, creds->session_token
    // Remember to call flb_aws_credentials_destroy(creds) when done
    flb_aws_credentials_destroy(creds);
}

// Clean up
flb_aws_provider_destroy(provider);
```

## Error Handling

All functions follow consistent error handling patterns:
- Return negative values on error, zero or positive on success
- Set errno when appropriate
- Log detailed error messages using flb_error()
- Ensure proper cleanup on error paths to prevent resource leaks
- Handle specific IMDS error conditions (timeouts, invalid responses)

## Security Considerations

1. **Network Security**: Uses proper HTTP client configuration
2. **Timeout Protection**: Specific timeouts prevent hanging connections
3. **Resource Cleanup**: Ensures no dangling connections or memory leaks
4. **Credential Handling**: Secure storage and transmission of credentials
5. **Access Control**: Proper validation of IMDS responses