# src/aws/CMakeLists.txt Documentation

## Overview

This CMakeLists.txt file is the build configuration for the AWS-specific components of Fluent Bit. It defines the source files, dependencies, and build targets for AWS integration features such as credential management, compression, and MSK IAM authentication.

## Key Components

### Source Files
The file defines the core AWS source files:
- Credential management (`flb_aws_credentials.c`, `flb_aws_credentials_ec2.c`, `flb_aws_credentials_http.c`)
- Utility functions (`flb_aws_util.c`)
- Compression functionality (`flb_aws_compress.c`)
- EC2 Instance Metadata Service (`flb_aws_imds.c`)
- STS (Security Token Service) support (`flb_aws_credentials_sts.c`)
- Profile-based credential loading (`flb_aws_credentials_profile.c`)
- MSK IAM authentication (`flb_aws_msk_iam.c`)
- Credential process support (`flb_aws_credentials_process.c`)

### Subdirectories
- `compression`: AWS-specific compression implementations

### Conditional Compilation
The file uses conditional compilation to include/exclude features:
- `FLB_HAVE_AWS_MSK_IAM`: Enables AWS MSK IAM authentication
- `FLB_HAVE_AWS_CREDENTIAL_PROCESS`: Enables credential process support

### Build Targets
Defines the `flb-aws` static library target that contains all AWS-specific functionality.

## Important Variables

- `src`: List of AWS source files to compile
- Various feature flags (FLB_HAVE_AWS_MSK_IAM, FLB_HAVE_AWS_CREDENTIAL_PROCESS)

## Dependencies and Relationships

This file is part of the AWS integration module and:
- Depends on the core Fluent Bit library
- Links to the AWS compression library (`flb-aws-compress`)
- Optionally links to rdkafka when MSK IAM is enabled
- Integrates with the main build system via `add_subdirectory()` in src/CMakeLists.txt

## Notable Implementation Details

1. **Modular Design**: Separates AWS functionality into its own library
2. **Conditional Features**: Enables/disables MSK IAM and credential process based on build flags
3. **Dependency Management**: Properly links required libraries (rdkafka for MSK IAM)
4. **Feature Detection**: Uses message() statements to report enabled features during build

## Usage Examples

To build with MSK IAM support:
```bash
cmake -DFLB_HAVE_AWS_MSK_IAM=ON ..
make
```

To build with credential process support:
```bash
cmake -DFLB_HAVE_AWS_CREDENTIAL_PROCESS=ON ..
make
```