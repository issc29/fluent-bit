# src/aws/flb_aws_credentials.c Documentation

## Overview

This C source file implements the AWS credentials management system for Fluent Bit. It provides a comprehensive framework for retrieving and managing AWS credentials from various sources, including environment variables, shared credentials files, EC2 Instance Metadata Service (IMDS), ECS credentials endpoint, and EKS IRSA.

## Key Components

### Standard Credential Provider Chain
Implements the standard AWS credential provider chain that follows the official AWS SDK precedence:
1. Environment variables
2. Shared credentials file (AWS Profile)
3. EKS OIDC
4. EC2 IMDS
5. ECS HTTP credentials endpoint

### Provider Types
- **Environment Provider**: Retrieves credentials from AWS environment variables
- **Profile Provider**: Reads credentials from AWS shared credentials file
- **EKS Provider**: Handles EKS OIDC and IRSA authentication
- **ECS Provider**: Retrieves credentials from ECS container credentials endpoint
- **EC2 Provider**: Fetches credentials from EC2 Instance Metadata Service

### Core Functions

### `flb_standard_chain_provider_create`
Creates the standard AWS credential provider chain.
- Handles EKS Fargate scenarios where EKS_POD_EXECUTION_ROLE is set
- Creates a chain of providers in the correct precedence order
- Returns a provider that can retrieve credentials from any source in the chain
- Supports automatic role assumption for EKS Fargate

### `flb_managed_chain_provider_create`
Creates a managed credential provider for output plugins.
- Automatically configures based on plugin properties
- Handles STS role assumption when role_arn is specified
- Manages TLS connections for secure credential retrieval
- Initializes credentials in sync mode and switches back to async
- Stores dependencies for proper cleanup

### `get_credentials_fn_environment`
Retrieves AWS credentials from environment variables.
- Checks for AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, and AWS_SESSION_TOKEN
- Returns credentials structure or NULL if not found
- Thread-safe implementation

### `flb_aws_credentials_destroy`
Properly frees an AWS credentials structure.
- Destroys all SDS strings (access_key_id, secret_access_key, session_token)
- Frees the credentials structure itself
- Safe to call with NULL pointer

### `flb_aws_provider_destroy`
Destroys an AWS provider and its dependencies.
- Calls provider-specific destroy function
- Cleans up mutex and managed dependencies (TLS instances, base providers)
- Handles managed dependencies cleanup

### `flb_aws_cred_expiration`
Parses and validates credential expiration timestamps.
- Converts ISO 8601 timestamp to Unix epoch time
- Performs sanity checks on expiration time
- Returns expiration time or -1 on error
- Logs warnings for suspicious expiration times

### `try_lock_provider` and `unlock_provider`
Thread synchronization functions for provider access.
- Prevents race conditions during credential refresh
- Uses pthread_mutex_trylock for non-blocking behavior
- Returns FLB_TRUE/FLB_FALSE for lock acquisition status

## Important Variables and Constants

### Environment Variables
- `AWS_ACCESS_KEY_ID`: AWS access key ID
- `AWS_SECRET_ACCESS_KEY`: AWS secret access key
- `AWS_SESSION_TOKEN`: Optional session token for temporary credentials
- `EKS_POD_EXECUTION_ROLE`: EKS Fargate execution role ARN

### Time Constants
- `FIVE_MINUTES`: 300 seconds (minimum reasonable credential expiration)
- `TWELVE_HOURS`: 43200 seconds (maximum reasonable credential expiration)

### Provider Chain Structure
```c
struct flb_aws_provider_chain {
    struct mk_list sub_providers;
    struct flb_aws_provider *sub_provider;
};
```
- Maintains list of sub-providers in precedence order
- Caches the currently active provider for efficiency
- Uses mk_list for provider management

### Provider VTable Structure
```c
struct flb_aws_provider_vtable {
    struct flb_aws_credentials *(*get_credentials)(struct flb_aws_provider *);
    int (*init)(struct flb_aws_provider *);
    int (*refresh)(struct flb_aws_provider *);
    void (*destroy)(struct flb_aws_provider *);
    void (*sync)(struct flb_aws_provider *);
    void (*async)(struct flb_aws_provider *);
    void (*upstream_set)(struct flb_aws_provider *, struct flb_output_instance *);
};
```
- Defines interface for all provider types
- Supports get_credentials, init, refresh, destroy, sync/async modes

## Dependencies and Relationships

This file depends on:
- Fluent Bit core utilities (flb_sds.h, flb_http_client.h, flb_aws_credentials.h)
- AWS utility functions (flb_aws_util.h)
- JSON parsing (flb_jsmn.h)
- Output plugin interface (flb_output_plugin.h)
- Standard C libraries (stdlib.h, time.h)
- Threading library (pthread.h)
- TLS utilities (flb_tls.h)
- AWS IMDS utilities (flb_aws_imds.h)

It integrates with:
- Individual credential providers (EC2, ECS, EKS, HTTP)
- STS provider for role assumption
- Output plugins that require AWS authentication
- TLS subsystem for secure connections

## Notable Implementation Details

1. **Thread Safety**: Uses mutex locks to prevent race conditions during credential refresh
2. **Provider Chaining**: Implements AWS SDK standard credential provider chain
3. **EKS Support**: Special handling for EKS Fargate and IRSA scenarios
4. **STS Integration**: Supports role assumption through STS provider
5. **Resource Management**: Proper cleanup of TLS instances and provider dependencies
6. **Error Handling**: Comprehensive error checking and reporting
7. **Memory Management**: Uses Fluent Bit's SDS for string management
8. **Asynchronous Support**: Sync/async mode switching for coroutine compatibility
9. **Timeout Handling**: Proper timeout configuration for network operations
10. **Logging**: Detailed debug and warning messages for troubleshooting
11. **Validation**: Input validation and sanity checks throughout
12. **Memory Safety**: Proper allocation and deallocation with error handling

## Usage Examples

The functions are typically called by AWS output plugins:
```c
#include <fluent-bit/flb_aws_credentials.h>

// Create standard credential provider chain
struct flb_aws_provider *provider = flb_standard_chain_provider_create(
    config, tls, region, sts_endpoint, proxy, generator, profile);

// Retrieve credentials
struct flb_aws_credentials *creds = provider->provider_vtable->get_credentials(provider);

// Use credentials for AWS API calls
if (creds) {
    // Use creds->access_key_id, creds->secret_access_key, creds->session_token
    // Remember to call flb_aws_credentials_destroy(creds) when done
    flb_aws_credentials_destroy(creds);
}

// Clean up
flb_aws_provider_destroy(provider);

// For managed providers (output plugins)
struct flb_aws_provider *managed_provider = flb_managed_chain_provider_create(
    ins, config, "aws_", proxy, generator);

// Use credentials
struct flb_aws_credentials *creds = managed_provider->provider_vtable->get_credentials(managed_provider);

// Clean up
flb_aws_provider_destroy(managed_provider);
```

## Error Handling

All functions follow consistent error handling patterns:
- Return negative values on error, zero or positive on success
- Set errno when appropriate
- Log detailed error messages using flb_error()
- Ensure proper cleanup on error paths to prevent resource leaks