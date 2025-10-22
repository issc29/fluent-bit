# Fluent Bit Main Configuration File

## File path: conf/fluent-bit.conf
## File type: Configuration file
## Purpose: This is the main configuration file for Fluent Bit, defining service settings, input plugins, filter plugins, and output plugins.

## Key components:
- `[SERVICE]` section: Defines global service settings for Fluent Bit
- `[INPUT]` section: Configures input plugins to collect data from various sources
- `[OUTPUT]` section: Configures output plugins to send data to various destinations
- Configuration directives with comments explaining their purpose

## Detailed documentation:

The `fluent-bit.conf` file serves as the primary configuration file for Fluent Bit. It follows an INI-style format with sections and key-value pairs.

### SERVICE Section
The `[SERVICE]` section defines global settings that affect the entire Fluent Bit instance:

- `flush`: Sets the interval in seconds before flushing records to a destination (default: 1)
- `daemon`: Controls whether Fluent Bit runs in foreground or background mode (default: Off)
- `log_level`: Sets the verbosity level (error, warning, info, debug, trace) (default: info)
- `parsers_file`: Specifies an optional parsers configuration file
- `plugins_file`: Specifies an optional plugins configuration file for external plugins
- `http_server`: Enables/disables the built-in HTTP server for metrics (default: Off)
- `http_listen`: Sets the HTTP server listening address (default: 0.0.0.0)
- `http_port`: Sets the HTTP server listening port (default: 2020)
- `storage.*`: Various storage-related settings for buffering and persistence

### INPUT Section
The `[INPUT]` section configures input plugins to collect data:

- `name`: Specifies the input plugin to use (e.g., cpu, tail, forward)
- `tag`: Sets the tag for the collected data
- Plugin-specific settings (e.g., interval_sec for the cpu input plugin)

### OUTPUT Section
The `[OUTPUT]` section configures output plugins to send data:

- `name`: Specifies the output plugin to use (e.g., stdout, es, kafka)
- `match`: Defines which tags this output should handle (using wildcards)

## Usage examples:

```ini
[SERVICE]
    flush        5
    daemon       Off
    log_level    info
    parsers_file parsers.conf
    http_server  On
    http_port    2020

[INPUT]
    name tail
    path /var/log/*.log
    tag  app.*

[OUTPUT]
    name es
    match app.*
    host elasticsearch
    port 9200
    index fluentbit

[FILTER]
    name parser
    match app.*
    key_name log
    parser json
```

Command-line usage:
```bash
# Use the configuration file
fluent-bit -c conf/fluent-bit.conf

# Override specific settings
fluent-bit -c conf/fluent-bit.conf -f 10 -v
```

## Dependencies:
- Fluent Bit runtime environment
- Referenced configuration files (parsers.conf, plugins.conf)
- Available input/output/filter plugins