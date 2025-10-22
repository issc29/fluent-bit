# Fluent Bit Library Core File

## File path: src/flb_lib.c
## File type: C source file
## Purpose: This file contains the core library functions for Fluent Bit, providing the programmatic interface for embedding Fluent Bit in applications.

## Key components:
- `flb_create()`: Creates a new Fluent Bit context
- `flb_destroy()`: Releases resources associated with a Fluent Bit context
- `flb_start()`: Starts the Fluent Bit engine
- `flb_stop()`: Stops the Fluent Bit engine
- `flb_input()`, `flb_output()`, `flb_filter()`: Functions to define input, output, and filter instances
- `flb_input_set()`, `flb_output_set()`, `flb_filter_set()`: Functions to set properties for instances
- `flb_lib_push()`: Pushes data into the Fluent Bit engine
- Context management functions

## Detailed documentation:

The `flb_lib.c` file provides the core library interface for Fluent Bit, allowing developers to embed Fluent Bit functionality directly into their applications. It contains essential functions for creating and managing Fluent Bit contexts, configuring plugins, and controlling the data processing pipeline.

### Context Management
The library provides functions for creating (`flb_create()`), destroying (`flb_destroy()`), starting (`flb_start()`), and stopping (`flb_stop()`) Fluent Bit contexts. Each context maintains its own configuration and processing pipeline.

### Plugin Management
Functions are provided to define and configure input, output, and filter plugins:
- `flb_input()`: Creates a new input instance
- `flb_output()`: Creates a new output instance
- `flb_filter()`: Creates a new filter instance
- `flb_input_set()`, `flb_output_set()`, `flb_filter_set()`: Set properties for respective instances

### Data Processing
The library provides functions for pushing data into the processing pipeline and handling responses:
- `flb_lib_push()`: Pushes data from input plugins into the engine
- `flb_lib_response()`: Handles responses from output plugins
- `flb_lib_free()`: Releases buffers allocated by the library

### Configuration and Validation
Functions for loading configuration files and validating plugin properties:
- `flb_lib_config_file()`: Loads a configuration file
- `flb_input_property_check()`, `flb_output_property_check()`, `flb_filter_property_check()`: Validate plugin configuration properties

### Testing Support
The library includes support for testing plugins:
- `flb_input_set_test()`, `flb_output_set_test()`: Enable test modes for plugins
- Test callback registration functions

## Usage examples:

```c
#include <fluent-bit/flb_lib.h>

int main() {
    flb_ctx_t *ctx;
    int in_ffd, out_ffd;
    
    // Create Fluent Bit context
    ctx = flb_create();
    if (!ctx) {
        return -1;
    }
    
    // Configure service properties
    flb_service_set(ctx, "flush", "1", "log_level", "info", NULL);
    
    // Create input instance
    in_ffd = flb_input(ctx, "dummy", NULL);
    flb_input_set(ctx, in_ffd, "tag", "dummy.data", NULL);
    
    // Create output instance
    out_ffd = flb_output(ctx, "stdout", NULL);
    flb_output_set(ctx, out_ffd, "match", "dummy.*", NULL);
    
    // Start the engine
    if (flb_start(ctx) == -1) {
        flb_destroy(ctx);
        return -1;
    }
    
    // Run for some time
    sleep(5);
    
    // Stop and cleanup
    flb_stop(ctx);
    flb_destroy(ctx);
    
    return 0;
}
```

For property validation:
```c
// Check if a property is valid for an input plugin
int ret = flb_input_property_check(ctx, in_ffd, "invalid_property", "value");
if (ret != 0) {
    printf("Invalid property\n");
}
```

## Dependencies:
- Standard C library headers
- CFL (C Fluent Library) for data structures
- Monkey Core library for event loops
- Fluent Bit internal libraries (flb_*.h headers)
- POSIX threading functions
- Platform-specific APIs (Windows Sockets, etc.)