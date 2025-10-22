# Fluent Bit Engine Core File

## File path: src/flb_engine.c
## File type: C source file
## Purpose: This file contains the core engine implementation for Fluent Bit, managing the event loop, plugin lifecycle, data routing, and overall processing pipeline.

## Key components:
- `flb_engine_start()`: Main engine startup function that initializes all components
- `flb_engine_shutdown()`: Engine shutdown function that cleans up resources
- `flb_engine_handle_event()`: Event handler for processing various engine events
- `flb_engine_flush()`: Function to flush data from input plugins
- Event loop management and plugin initialization
- Task scheduling and retry mechanisms
- Data routing between input, filter, and output plugins

## Detailed documentation:

The `flb_engine.c` file implements the core engine of Fluent Bit, responsible for orchestrating the entire data processing pipeline. It manages the event loop, plugin lifecycle, data routing, and overall system coordination.

### Engine Lifecycle
The engine follows a well-defined lifecycle:
1. **Initialization**: Sets up event loops, networking, storage, and plugin systems
2. **Startup**: Initializes all plugins (input, filter, output) and starts data collection
3. **Runtime**: Processes events, routes data, and manages retries
4. **Shutdown**: Gracefully stops processing and cleans up resources

### Event Management
The engine uses an event-driven architecture with multiple event types:
- Core events (engine control, flush requests)
- Input events (data collection from input plugins)
- Output events (task completion notifications)
- Scheduler events (retry scheduling, timer callbacks)
- Thread events (coroutine management)
- Custom events (plugin-specific events)

### Data Processing Pipeline
The engine manages the complete data flow:
1. **Input Collection**: Receives data from input plugins via event loop
2. **Routing**: Uses the router to match input tags with appropriate outputs
3. **Filtering**: Processes data through configured filter plugins
4. **Output Dispatch**: Sends data to output plugins
5. **Task Management**: Tracks and manages data chunks through the pipeline

### Retry and Error Handling
The engine implements sophisticated retry mechanisms:
- Automatic retry scheduling for failed outputs
- Configurable retry limits and backoff strategies
- Graceful handling of persistent failures
- Backlog management for filesystem-stored chunks

### Resource Management
The engine handles various resources:
- Event loops and file descriptors
- Memory and filesystem buffering
- Network connections and DNS resolution
- Plugin contexts and configurations
- Metrics collection and reporting

## Usage examples:

The engine is typically started through the main application:

```c
// In fluent-bit.c
int flb_main_run(int argc, char **argv) {
    // ... configuration setup ...
    
    // Start the engine
    ret = flb_start(ctx);
    if (ret != 0) {
        // Handle error
    }
    
    // Run the event loop
    ret = flb_loop(ctx);
    
    // Cleanup
    flb_stop(ctx);
    flb_destroy(ctx);
    
    return ret;
}
```

For programmatic usage:

```c
#include <fluent-bit/flb_lib.h>

int main() {
    flb_ctx_t *ctx;
    
    ctx = flb_create();
    // Configure plugins...
    
    if (flb_start(ctx) == 0) {
        // Engine is running
        sleep(10); // Process for 10 seconds
        flb_stop(ctx);
    }
    
    flb_destroy(ctx);
    return 0;
}
```

## Dependencies:
- Monkey Core library for event loops
- CFL (C Fluent Library) for data structures
- Fluent Bit internal libraries (flb_*.h headers)
- POSIX threading and networking functions
- Platform-specific APIs for Windows/Unix compatibility