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

### Provider Functions

### `flb_ec2_provider_create`
Creates and initializes an EC2 credential provider.
- Sets up HTTP client for IMDS communication
- Configures IMDS interface with default settings
- Initializes mutex for thread safety
- Returns provider ready for credential retrieval

### `get_credentials_fn_ec2`
Retrieves AWS credentials from EC2 IMDS.
- Checks if cached credentials are expired or missing
- Uses mutex to prevent race conditions during refresh
- Returns deep copy of credentials for safe usage

### `refresh_fn_ec2`
Forces refresh of EC2 credentials.
- Acquires mutex and calls internal refresh function
- Updates cached credentials and refresh timing

### `init_fn_ec2`
Initializes EC2 provider with credentials.
- Performs initial credential fetch
- Sets up proper timeouts for IMDS communication

### `sync_fn_ec2` / `async_fn_ec2`
Controls synchronous/asynchronous operation mode.
- Enables/disables async mode for HTTP client
- Used for coroutine compatibility

### `upstream_set_fn_ec2`
Configures upstream connection for EC2 provider.
- Sets up HTTP client with proper timeouts
- Ensures IMDS-specific network configuration

### `destroy_fn_ec2`
Cleans up EC2 provider resources.
- Destroys cached credentials
- Frees IMDS interface and HTTP client
- Releases mutex and provider structure

## Important Variables and Constants

### IMDS Paths
- `AWS_IMDS_ROLE_PATH`: "/latest/meta-data/iam/security-credentials/" - Base path for role information
- `AWS_IMDS_ROLE_PATH_LEN`: 43 - Length of the base path

### Timing Constants
- `FLB_AWS_IMDS_TIMEOUT`: Timeout for IMDS requests
- `FLB_AWS_REFRESH_WINDOW`: Time window before expiration to refresh credentials

## Dependencies and Relationships

This file depends on:
- Fluent Bit core utilities (flb_sds.h, flb_http_client.h, flb_aws_credentials.h)
- AWS utility functions (flb_aws_util.h)
- JSON parsing (flb_jsmn.h)
- IMDS interface (flb_aws_imds.h)
- Standard C libraries (stdlib.h, time.h, sys/types.h, sys/stat.h)
- Threading library (pthread.h)

It integrates with:
- Standard credential provider chain
- IMDS interface for metadata communication
- HTTP client for secure communication
- Output plugins requiring EC2 credentials

## Notable Implementation Details

1. **IMDS Protocol Support**: Handles both IMDSv1 and IMDSv2 protocols
2. **Thread Safety**: Uses mutex locks to prevent race conditions
3. **Credential Caching**: Caches credentials with automatic refresh timing
4. **Network Configuration**: Properly configures timeouts for IMDS communication
5. **Resource Management**: Comprehensive cleanup of all allocated resources
6. **Error Handling**: Robust error checking with appropriate logging
7. **Coroutine Compatibility**: Sync/async mode switching for coroutine support
8. **Security**: Uses proper HTTP client configuration for secure communication

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
}

// Clean up
flb_aws_provider_destroy(provider);
```