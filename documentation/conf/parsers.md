# Fluent Bit Parser Configuration File

## File path: conf/parsers.conf
## File type: Configuration file
## Purpose: This file contains predefined parser configurations for various log formats, enabling Fluent Bit to parse structured data from unstructured log inputs.

## Key components:
- Multiple `[PARSER]` sections for different log formats
- Parser definitions with `Name`, `Format`, `Regex`, and time-related settings
- Predefined parsers for common formats (Apache, Nginx, JSON, Docker, etc.)

## Detailed documentation:

The `parsers.conf` file defines parser configurations that Fluent Bit uses to transform unstructured log data into structured records. Each parser is defined in its own `[PARSER]` section with specific settings.

### Parser Configuration Parameters

Each parser section can contain the following parameters:

- `Name`: Unique identifier for the parser
- `Format`: Specifies the parsing format (regex, json, logfmt)
- `Regex`: Regular expression pattern for regex-based parsing
- `Time_Key`: Field name containing timestamp information
- `Time_Format`: Format string for parsing timestamps
- `Time_Keep`: Whether to retain the original timestamp field (On/Off)

### Predefined Parsers

The file includes parsers for common log formats:

1. **Apache/Nginx Web Servers**: Parsers for access logs and error logs
2. **JSON Logs**: Parser for JSON-formatted logs
3. **Docker Logs**: Parsers for Docker container logs
4. **Syslog**: Parsers for RFC 3164 and RFC 5424 syslog formats
5. **MongoDB**: Parser for MongoDB log format
6. **Envoy Proxy**: Parsers for Envoy proxy access logs
7. **Kubernetes**: Parser for CRI (Container Runtime Interface) logs
8. **Custom Formats**: Parsers for specialized log formats

### Parser Types

1. **Regex Parsers**: Use regular expressions to extract fields from log lines
2. **JSON Parsers**: Parse JSON-formatted log entries
3. **Logfmt Parsers**: Parse logfmt-formatted logs (key=value pairs)

## Usage examples:

```ini
# Reference the parser in an input configuration
[INPUT]
    name tail
    path /var/log/apache2/access.log
    parser apache
    tag apache.access

# Or in a filter configuration
[FILTER]
    name parser
    match app.*
    key_name log
    parser json
```

Command-line usage:
```bash
# Specify custom parser file
fluent-bit -c fluent-bit.conf -R /path/to/custom-parsers.conf

# Or reference in main config
# In fluent-bit.conf:
# parsers_file /path/to/parsers.conf
```

## Dependencies:
- Fluent Bit runtime environment
- Referenced in main configuration via `parsers_file` directive
- Used by input plugins (tail, systemd) and filter plugins (parser)