# lib/flb_libco

## Overview

flb_libco is a fork of the original libco library created by Byuu, specifically adapted for use in Fluent Bit. It provides a lightweight, portable coroutine library that enables cooperative multitasking in C applications.

Coroutines allow multiple execution contexts to run within a single thread, switching between them cooperatively rather than through preemptive scheduling. This makes them particularly useful for I/O-bound operations and event-driven architectures.

The library has been enhanced with several improvements for Fluent Bit's needs:
- ARMv8: Workaround for GCC bug
- Added aarch64.c backend file
- Fixes on settings.h to get MacOS support
- Extended co_create() API to retrieve the real size of the stack created

## Key Methods/Functions

### Thread Management
- `co_active()` - Get the currently active coroutine thread
- `co_create()` - Create a new coroutine thread with specified stack size and entry function
- `co_delete()` - Delete a coroutine thread and free associated resources
- `co_switch()` - Switch execution to a specified coroutine thread

### Thread Types
- `cothread_t` - Type definition for coroutine thread handles

## Important Usage Notes

1. **Coroutine Creation**: Use `co_create()` to create new coroutine threads. The function requires the stack size, entry function, and a pointer to store the actual stack size allocated.
2. **Thread Switching**: Use `co_switch()` to transfer control between coroutine threads. The calling thread will be suspended until another thread switches back to it.
3. **Active Thread**: `co_active()` returns the currently executing coroutine thread, which is useful for self-referencing operations.
4. **Resource Cleanup**: Always use `co_delete()` to properly clean up coroutine threads when they are no longer needed to prevent memory leaks.
5. **Stack Size**: Be mindful of stack size allocation. Too small and you'll get stack overflow; too large and you waste memory. The library returns the actual allocated size.
6. **Cooperative Scheduling**: Coroutines are cooperative, meaning they must explicitly yield control to other threads. There's no automatic preemption.
7. **No Preemption**: Unlike threads, coroutines don't run concurrently. They run sequentially, with explicit switches between them.
8. **Lightweight**: Coroutines are much lighter than OS threads, making them suitable for creating thousands of concurrent tasks.
9. **Platform Support**: The library supports multiple architectures including x86, x86-64, ARM, PowerPC, and others through architecture-specific implementations.
10. **Error Handling**: The library has minimal error handling. Invalid operations may result in undefined behavior.
11. **Memory Management**: The library manages its own memory for coroutine stacks, but users must properly delete threads when done.
12. **Use Cases**: Ideal for I/O multiplexing, state machines, generators, and event-driven programming models.