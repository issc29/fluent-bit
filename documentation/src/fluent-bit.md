# Fluent Bit Main Application File

## File path: src/fluent-bit.c
## File type: C source file
## Purpose: This file contains the main entry point for the Fluent Bit application, including command-line argument parsing, signal handling, and the main execution loop.

## Key components:
- `flb_main_run()`: Main execution function that handles command-line arguments and runs the Fluent Bit engine
- `flb_help()`: Function to display help information and available plugins
- `flb_signal_handler()`: Signal handling for graceful shutdown and other signals
- `main()`: Entry point function that delegates to platform-specific implementations
- Command-line option parsing and processing
- Configuration loading and validation
- Plugin initialization and management

## Detailed documentation:

The `fluent-bit.c` file serves as the main entry point for the Fluent Bit application. It contains the essential functionality for:

1. **Command-line argument parsing**: The file implements comprehensive command-line option parsing using `getopt_long()` to handle various configuration options.

2. **Signal handling**: Implements signal handlers for SIGINT, SIGTERM, SIGSEGV, and other signals to ensure graceful shutdown and proper error handling.

3. **Configuration management**: Handles loading of configuration files, environment variables, and command-line overrides.

4. **Plugin system integration**: Interfaces with the Fluent Bit plugin system to load and manage input, output, filter, and processor plugins.

5. **Main execution loop**: Contains the core event loop that keeps Fluent Bit running and processing data.

The file also includes platform-specific code for Windows and Unix-like systems, ensuring cross-platform compatibility.

## Usage examples:

```bash
# Basic usage with configuration file
fluent-bit -c /path/to/fluent-bit.conf

# Run in daemon mode
fluent-bit -c /path/to/fluent-bit.conf -d

# Specify storage path
fluent-bit -c /path/to/fluent-bit.conf -b /var/log/fluent-bit

# Set flush interval
fluent-bit -c /path/to/fluent-bit.conf -f 5

# Enable HTTP monitoring server
fluent-bit -c /path/to/fluent-bit.conf -H

# Dry run to test configuration
fluent-bit -c /path/to/fluent-bit.conf -D
```

For programmatic usage:

```c
#include <fluent-bit/flb_lib.h>

int main(int argc, char **argv) {
    return flb_main(argc, argv);
}
```

## Dependencies:
- Standard C library headers (stdio.h, stdlib.h, string.h, etc.)
- CFL (C Fluent Library) for data structures and utilities
- Monkey Core library (mk_core_init())
- Fluent Bit internal libraries (flb_*.h headers)
- POSIX signal handling functions
- Platform-specific APIs (Windows Console API, etc.)