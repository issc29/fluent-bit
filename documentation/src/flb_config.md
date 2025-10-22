# Fluent Bit Configuration Management File

## File path: src/flb_config.c
## File type: C source file
## Purpose: This file contains the configuration management system for Fluent Bit, handling configuration parsing, validation, and plugin initialization.

## Key components:
- `flb_config_init()`: Initializes a new configuration context
- `flb_config_exit()`: Cleans up and releases configuration resources
- `flb_config_set_property()`: Sets configuration properties dynamically
- `flb_config_load_config_format()`: Loads configuration from various formats
- Configuration property mapping system
- Plugin configuration and initialization
- Environment variable handling

## Detailed documentation:

The `flb_config.c` file implements the configuration management system for Fluent Bit. It handles the creation, loading, validation, and cleanup of configuration data that drives the entire Fluent Bit instance.

### Configuration Structure
The file defines and manages the `flb_config` structure, which contains all configuration data including:
- Service-level settings (flush interval, daemon mode, log level, etc.)
- Plugin configurations (input, filter, output, custom)
- Environment variables and metadata
- Storage and networking settings
- Routing and scheduling configurations

### Property Management
The configuration system includes a comprehensive property mapping mechanism:
- Static service configuration definitions (`service_configs` array)
- Dynamic property setting through `flb_config_set_property()`
- Type-safe property handling (int, double, bool, string)
- Environment variable substitution
- Log level configuration with multiple verbosity levels

### Plugin Configuration
The file handles plugin configuration and initialization:
- Input plugin configuration and instantiation
- Filter plugin configuration and instantiation
- Output plugin configuration and instantiation
- Custom plugin configuration and instantiation
- Processor configuration for input/output plugins
- Property validation for each plugin type

### Configuration Loading
Supports multiple configuration formats:
- Classic INI-style configuration
- YAML configuration format
- Environment variable overrides
- Parser configuration files
- Plugin configuration files

### Resource Management
The configuration system manages various resources:
- Memory allocation and deallocation
- File descriptor management
- Event loop integration
- Thread safety and synchronization
- Cleanup of all allocated resources

## Usage examples:

```c
#include <fluent-bit/flb_config.h>

// Create a new configuration context
struct flb_config *config = flb_config_init();
if (!config) {
    // Handle error
}

// Set configuration properties
flb_config_set_property(config, "flush", "5");
flb_config_set_property(config, "log_level", "info");
flb_config_set_property(config, "storage_path", "/tmp/storage");

// Load configuration from file
struct flb_cf *cf = flb_cf_create_from_file(NULL, "fluent-bit.conf");
if (cf) {
    flb_config_load_config_format(config, cf);
}

// Cleanup when done
flb_config_exit(config);
```

For programmatic plugin configuration:
```c
// Configure an input plugin
struct flb_input_instance *in = flb_input_new(config, "tail", NULL, FLB_TRUE);
if (in) {
    flb_input_set_property(in, "path", "/var/log/*.log");
    flb_input_set_property(in, "tag", "app.log");
}

// Configure an output plugin
struct flb_output_instance *out = flb_output_new(config, "stdout", NULL, FLB_TRUE);
if (out) {
    flb_output_set_property(out, "match", "app.*");
}
```

## Dependencies:
- CFL (C Fluent Library) for data structures
- Monkey Core library for event loops
- Fluent Bit internal libraries (flb_*.h headers)
- POSIX threading and networking functions
- Standard C library for memory and string operations