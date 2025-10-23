# src/aws/flb_aws_credentials_profile.c Documentation

## Overview

This file implements the AWS profile credentials provider for Fluent Bit. It provides functionality to retrieve AWS credentials from shared configuration files (typically located at `~/.aws/config` and `~/.aws/credentials`). The module handles parsing of these files, profile selection, and credential extraction according to AWS conventions.

The provider supports:
- Reading credentials from shared credentials files
- Reading credentials from shared config files with credential_process support
- Profile selection via environment variables or defaults
- Proper credential lifecycle management

## Key Functions

### `flb_profile_provider_create`
Creates a new AWS profile credentials provider.
- **Parameters**: 
  - `profile`: Optional profile name (if NULL, uses environment variables or defaults)
- **Return Value**: Pointer to newly created provider or NULL on failure
- **Description**: Initializes the provider with config and credentials file paths, sets up profile name resolution

### `get_credentials_fn_profile`
Retrieves current AWS credentials from the provider.
- **Parameters**: 
  - `provider`: Pointer to the provider instance
- **Return Value**: Pointer to credentials structure or NULL on failure
- **Description**: Checks if credentials need refreshing and returns current credentials

### `refresh_fn_profile`
Explicitly refreshes AWS credentials from the provider.
- **Parameters**: 
  - `provider`: Pointer to the provider instance
- **Return Value**: 0 on success, -1 on failure
- **Description**: Forces credential refresh regardless of expiration status

### `init_fn_profile`
Initializes the provider and retrieves initial credentials.
- **Parameters**: 
  - `provider`: Pointer to the provider instance
- **Return Value**: 0 on success, -1 on failure
- **Description**: Performs initial credential retrieval during provider setup

### `destroy_fn_profile`
Destroys the provider and cleans up resources.
- **Parameters**: 
  - `provider`: Pointer to the provider instance
- **Return Value**: None
- **Description**: Frees all allocated memory and releases provider resources

### `refresh_credentials`
Internal function to refresh credentials from files.
- **Parameters**: 
  - `implementation`: Pointer to provider implementation
  - `debug_only`: Flag to control error reporting level
- **Return Value**: 0 on success, -1 on failure
- **Description**: Reads and parses credentials from config and credentials files

### `get_aws_shared_file_path`
Resolves the path to AWS shared configuration files.
- **Parameters**: 
  - `field`: Output parameter for resolved path
  - `env_var`: Environment variable name (AWS_CONFIG_FILE or AWS_SHARED_CREDENTIALS_FILE)
  - `home_aws_path`: Default path relative to HOME
- **Return Value**: 0 on success, -1 on failure
- **Description**: Determines file path from environment variables or defaults

### `parse_config_file`
Parses AWS shared config file contents.
- **Parameters**: 
  - `buf`: Buffer containing file contents
  - `profile`: Target profile name
  - `creds`: Output parameter for parsed credentials
  - `expiration`: Output parameter for credential expiration time
  - `debug_only`: Flag to control error reporting level
- **Return Value**: 0 on success, -1 on failure
- **Description**: Extracts credentials from config file, supports credential_process

### `parse_credentials_file`
Parses AWS shared credentials file contents.
- **Parameters**: 
  - `buf`: Buffer containing file contents
  - `profile`: Target profile name
  - `creds`: Output parameter for parsed credentials
  - `debug_only`: Flag to control error reporting level
- **Return Value**: 0 on success, -1 on failure
- **Description**: Extracts access key, secret key, and session token from credentials file

## Important Variables

### `ACCESS_KEY_PROPERTY_NAME`
Constant string "aws_access_key_id" used to identify access key properties in files.

### `SECRET_KEY_PROPERTY_NAME`
Constant string "aws_secret_access_key" used to identify secret key properties in files.

### `SESSION_TOKEN_PROPERTY_NAME`
Constant string "aws_session_token" used to identify session token properties in files.

### `CREDENTIAL_PROCESS_PROPERTY_NAME`
Constant string "credential_process" used to identify credential process commands in config files.

### `AWS_PROFILE`
Environment variable name "AWS_PROFILE" for specifying the AWS profile to use.

### `AWS_DEFAULT_PROFILE`
Environment variable name "AWS_DEFAULT_PROFILE" for specifying the default AWS profile.

### `AWS_CONFIG_FILE`
Environment variable name "AWS_CONFIG_FILE" for specifying the AWS config file path.

### `AWS_SHARED_CREDENTIALS_FILE`
Environment variable name "AWS_SHARED_CREDENTIALS_FILE" for specifying the AWS credentials file path.

### `DEFAULT_PROFILE`
Default profile name "default" used when no profile is specified.

### `CONFIG_PROFILE_PREFIX`
String "profile " used to identify profile sections in config files.

## Dependencies

- AWS credentials logging utilities (`flb_aws_credentials_log.h`)
- AWS credentials structures and functions (`flb_aws_credentials.h`)
- AWS utility functions (`flb_aws_util.h`)
- HTTP client functionality (`flb_http_client.h`)
- Fluent Bit information and SDS string functions (`flb_info.h`, `flb_sds.h`)
- Standard C library headers (stdlib.h, string.h, time.h, sys/types.h, sys/stat.h, ctype.h)

## Implementation Details

### Provider Architecture
The module implements the AWS credentials provider interface with:
- A provider structure that holds configuration and state
- Virtual table of function pointers for provider operations
- Thread-safe credential access with mutex locking
- Automatic credential refresh based on expiration times

### File Path Resolution
Resolves AWS configuration file paths in this order:
1. Environment variables (AWS_CONFIG_FILE, AWS_SHARED_CREDENTIALS_FILE)
2. HOME environment variable with default paths (~/.aws/config, ~/.aws/credentials)
3. Falls back to NULL if neither is available

### Profile Selection Logic
Determines which profile to use in this order:
1. Explicit profile parameter to flb_profile_provider_create
2. AWS_PROFILE environment variable
3. AWS_DEFAULT_PROFILE environment variable
4. Default "default" profile

### Credential Parsing Process
For credentials files:
- Parses INI-style format with [profile_name] sections
- Extracts aws_access_key_id, aws_secret_access_key, and aws_session_token properties
- Validates that both access key and secret key are present

For config files:
- Supports credential_process property for external credential retrieval
- Falls back to shared credentials file if credential_process is not found
- Handles both [profile name] and [default] section formats

### Memory Management
- Uses Fluent Bit's SDS (String Data Structure) for dynamic strings
- Properly allocates and frees memory for provider structures
- Cleans up credentials on refresh and destruction
- Handles buffer allocation for file reading operations

### Thread Safety
- Uses mutex locks to ensure thread-safe credential access
- Prevents concurrent refresh operations
- Atomic credential updates to avoid race conditions

### Error Handling
Comprehensive error handling with:
- Detailed logging for debugging purposes
- Graceful fallback between config and credentials files
- Proper cleanup on parsing failures
- Informative error messages for common issues

## Usage Examples

To create a profile provider and retrieve credentials:
```c
#include <fluent-bit/aws/flb_aws_credentials_profile.h>
#include <fluent-bit/flb_aws_credentials.h>

// Create provider with default profile
struct flb_aws_provider *provider = flb_profile_provider_create(NULL);

if (provider) {
    // Initialize the provider
    if (flb_aws_provider_init(provider) == 0) {
        // Retrieve credentials
        struct flb_aws_credentials *creds = flb_aws_provider_get_credentials(provider);
        if (creds) {
            // Use credentials
            printf("Access Key: %s\n", creds->access_key_id);
            printf("Secret Key: %s\n", creds->secret_access_key);
            
            // Clean up credentials
            flb_aws_credentials_destroy(creds);
        }
        
        // Clean up provider
        flb_aws_provider_destroy(provider);
    }
}
```

To create a provider with a specific profile:
```c
#include <fluent-bit/aws/flb_aws_credentials_profile.h>

// Create provider with specific profile
struct flb_aws_provider *provider = flb_profile_provider_create("my-custom-profile");

if (provider) {
    // Use the provider as shown above
    // ...
}
```

Example AWS credentials file (~/.aws/credentials):
```ini
[default]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

[my-custom-profile]
aws_access_key_id = AKIAI44QH8DHBEXAMPLE
aws_secret_access_key = je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
aws_session_token = AQoDYXdzEPT//////////wEXAMPLEtc764bNrC9SAPBSM22wDOk4x4HIZ8j4FZTwdQW
```

Example AWS config file (~/.aws/config):
```ini
[default]
region = us-west-2
output = json

[profile my-custom-profile]
region = us-east-1
output = text
credential_process = /opt/bin/aws-credentials-provider --profile my-custom-profile
```