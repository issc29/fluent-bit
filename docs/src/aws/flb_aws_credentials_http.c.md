# src/aws/flb_aws_credentials_http.c Documentation

## Overview

This C source file implements the HTTP-based credential provider for Fluent Bit's AWS integration. It provides functionality to retrieve temporary AWS credentials from various HTTP endpoints including ECS container credentials, EKS IRSA, and custom HTTP endpoints.

## Key Components

### HTTP Provider Structure
```c
struct flb_aws_provider_http {
    struct flb_aws_credentials *creds;
    time_t next_refresh;
    struct flb_aws_client *client;
    flb_sds_t host;
    flb_sds_t path;
};
```
- Stores cached credentials and refresh timing
- Manages HTTP client for credential endpoint communication
- Tracks endpoint host and path information

### Provider Types
- **ECS Provider**: Retrieves credentials from ECS container credentials endpoint
- **EKS Provider**: Handles EKS IRSA authentication via HTTP endpoint
- **Custom HTTP Provider**: Generic provider for custom HTTP credential endpoints

### Core Functions

### `flb_http_provider_create`
Creates an ECS/EKS HTTP credential provider.
- Detects ECS/EKS environment through environment variables
- Validates credential endpoint URIs for security
- Creates appropriate HTTP provider based on detected environment

### `flb_endpoint_provider_create`
Creates a generic HTTP credential provider.
- Configures HTTP client for specified endpoint
- Handles both HTTP and HTTPS connections
- Sets up proper network timeouts

### `get_credentials_fn_http`
Retrieves AWS credentials from HTTP endpoint.
- Checks if cached credentials are expired or missing
- Uses mutex to prevent race conditions during refresh
- Returns deep copy of credentials for safe usage

### `refresh_fn_http`
Forces refresh of HTTP credentials.
- Acquires mutex and calls internal refresh function
- Updates cached credentials and refresh timing

### `init_fn_http`
Initializes HTTP provider with credentials.
- Performs initial credential fetch
- Sets up proper timeouts for HTTP communication

### `sync_fn_http` / `async_fn_http`
Controls synchronous/asynchronous operation mode.
- Enables/disables async mode for HTTP client
- Used for coroutine compatibility

### `upstream_set_fn_http`
Configures upstream connection for HTTP provider.
- Sets up HTTP client with proper network configuration

### `destroy_fn_http`
Cleans up HTTP provider resources.
- Destroys cached credentials
- Frees HTTP client and endpoint information
- Releases mutex and provider structure

### `http_credentials_request`
Performs HTTP request to retrieve credentials.
- Handles authorization tokens from environment or file
- Makes HTTP GET request to credential endpoint
- Parses JSON response into credentials structure

### `flb_parse_http_credentials`
Parses HTTP credential response JSON.
- Extracts access key, secret key, session token, and expiration
- Validates required fields are present
- Converts expiration timestamp to Unix time

### `validate_http_credential_uri`
Validates credential endpoint URI for security.
- Ensures HTTPS endpoints or loopback/link-local addresses
- Prevents credential leakage to unauthorized endpoints

## Important Variables and Constants

### Environment Variables
- `AWS_CREDENTIALS_RELATIVE_URI`: ECS relative URI for credential endpoint
- `AWS_CREDENTIALS_FULL_URI`: Full URI for credential endpoint
- `AUTH_TOKEN_ENV_VAR`: Authorization token for credential endpoint
- `AUTH_TOKEN_FILE_ENV_VAR`: File containing authorization token

### Endpoint Hosts
- `ECS_CREDENTIALS_HOST`: "169.254.170.2" - ECS container host
- `EKS_CREDENTIALS_HOST`: "169.254.170.23" - EKS container host

### JSON Field Names
- `AWS_CREDENTIAL_RESPONSE_ACCESS_KEY`: "AccessKeyId"
- `AWS_CREDENTIAL_RESPONSE_SECRET_KEY`: "SecretAccessKey"
- `AWS_HTTP_RESPONSE_TOKEN`: "Token"
- `AWS_CREDENTIAL_RESPONSE_EXPIRATION`: "Expiration"

## Dependencies and Relationships

This file depends on:
- Fluent Bit core utilities (flb_sds.h, flb_http_client.h, flb_aws_credentials.h)
- AWS utility functions (flb_aws_util.h)
- JSON parsing (flb_jsmn.h)
- Standard C libraries (stdlib.h, time.h, string.h, sys/types.h, sys/stat.h)
- Threading library (pthread.h)

It integrates with:
- Standard credential provider chain
- HTTP client for secure communication
- Output plugins requiring container credentials

## Notable Implementation Details

1. **Security Validation**: Strict validation of credential endpoint URIs
2. **Authorization Support**: Handles authorization tokens from env vars or files
3. **Thread Safety**: Uses mutex locks to prevent race conditions
4. **Credential Caching**: Caches credentials with automatic refresh timing
5. **Environment Detection**: Automatically detects ECS/EKS environments
6. **JSON Parsing**: Robust JSON parsing with error handling
7. **Network Configuration**: Proper timeout and connection settings
8. **Resource Management**: Comprehensive cleanup of all allocated resources
9. **Coroutine Compatibility**: Sync/async mode switching for coroutine support

## Usage Examples

The functions are typically called by the standard credential provider chain:
```c
#include <fluent-bit/flb_aws_credentials.h>

// Create HTTP provider (typically done by standard chain)
struct flb_aws_provider *provider = flb_http_provider_create(config, generator);

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