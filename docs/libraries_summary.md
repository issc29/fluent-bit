# Libraries Quick Reference Guide

This document provides a quick reference guide for developers working with the key libraries used in this project.

## lib/jansson-e23f558

### Overview

Jansson is a C library for encoding, decoding and manipulating JSON data. Its main features and design principles are:

- Simple and intuitive API and data model
- Comprehensive documentation
- No dependencies on other libraries
- Full Unicode support (UTF-8)
- Extensive test suite

Jansson is licensed under the MIT license.

### Key Methods/Functions

**Construction and Reference Counting**
- `json_object()` - Create a new JSON object
- `json_array()` - Create a new JSON array
- `json_string()` - Create a new JSON string
- `json_integer()` - Create a new JSON integer
- `json_real()` - Create a new JSON real number
- `json_true()` - Create a new JSON true value
- `json_false()` - Create a new JSON false value
- `json_null()` - Create a new JSON null value
- `json_incref()` - Increment reference count of a JSON value
- `json_decref()` - Decrement reference count of a JSON value

**Object Operations**
- `json_object_size()` - Get the number of items in a JSON object
- `json_object_get()` - Get a value from a JSON object by key
- `json_object_set_new()` - Set a value in a JSON object with new value
- `json_object_del()` - Delete a value from a JSON object by key
- `json_object_clear()` - Clear all items from a JSON object
- `json_object_update()` - Update a JSON object with items from another object

**Array Operations**
- `json_array_size()` - Get the number of items in a JSON array
- `json_array_get()` - Get a value from a JSON array by index
- `json_array_append_new()` - Append a new value to a JSON array
- `json_array_remove()` - Remove a value from a JSON array at a specific index
- `json_array_clear()` - Clear all items from a JSON array

**Loading and Dumping**
- `json_loads()` - Load JSON from a string
- `json_load_file()` - Load JSON from a file path
- `json_dumps()` - Dump JSON to a string
- `json_dump_file()` - Dump JSON to a file path

### Important Usage Notes

1. **Reference Counting**: Jansson uses reference counting for memory management. Always call `json_decref()` when you're done with a JSON value to prevent memory leaks.

2. **Thread Safety**: Jansson is thread-safe for all read-only functions plus reference counting operations.

3. **UTF-8 Support**: Jansson has full Unicode support with UTF-8 encoding. All strings are assumed to be valid UTF-8.

4. **API Simplicity**: Jansson provides a simple and intuitive API for working with JSON data.

5. **No Dependencies**: Jansson has no external dependencies, making it easy to integrate into projects.

## lib/jemalloc-5.3.0

### Overview

jemalloc is a general purpose malloc(3) implementation that emphasizes fragmentation avoidance and scalable concurrency support. jemalloc first came into use as the FreeBSD libc allocator in 2005, and since then it has found its way into numerous applications that rely on its predictable behavior.

### Key Methods/Functions

**Standard Memory Allocation**
- `malloc(size_t size)` - Allocate memory block
- `calloc(size_t num, size_t size)` - Allocate and zero-initialize memory block
- `realloc(void *ptr, size_t size)` - Reallocate memory block
- `free(void *ptr)` - Deallocate memory block

**Extended Allocation**
- `mallocx(size_t size, int flags)` - Extended memory allocation
- `rallocx(void *ptr, size_t size, int flags)` - Extended memory reallocation
- `xallocx(void *ptr, size_t size, size_t extra, int flags)` - Extend memory allocation
- `sallocx(const void *ptr, int flags)` - Get allocation size
- `dallocx(void *ptr, int flags)` - Extended memory deallocation

**Control Interface**
- `mallctl(const char *name, void *oldp, size_t *oldlenp, void *newp, size_t newlen)` - Control interface
- `malloc_stats_print(void (*write_cb)(void *, const char *), void *cbopaque, const char *opts)` - Print statistics

**Arena Management**
- `arenas.create(unsigned *arena_ind)` - Create new arena
- `arenas.destroy(unsigned arena_ind)` - Destroy arena

### Important Usage Notes

1. **Drop-in Replacement**: jemalloc is designed to be a drop-in replacement for the standard C library's malloc implementation.

2. **Thread Safety**: jemalloc is fully thread-safe and designed for concurrent access from multiple threads.

3. **Scalability**: jemalloc uses multiple arenas to reduce lock contention and improve scalability on multi-core systems.

4. **Memory Efficiency**: jemalloc focuses on minimizing memory fragmentation through its sophisticated allocation strategies.

5. **Tuning Options**: jemalloc provides extensive runtime tuning options through the mallctl interface and environment variables.

6. **Profiling Support**: jemalloc includes built-in heap profiling capabilities for memory usage analysis.

7. **Background Threads**: jemalloc can use background threads for memory purging operations to improve application responsiveness.

## lib/jsmn

### Overview

jsmn (pronounced like 'jasmine') is a minimalistic JSON parser in C. It can be easily integrated into resource-limited or embedded projects. jsmn is designed to be robust (it should work fine even with erroneous data), fast (it should parse data on the fly), portable (no superfluous dependencies or non-standard C extensions), and simple (simple code style, simple algorithm, simple integration into other projects).

### Key Methods/Functions

**Core API**
- `jsmn_init(jsmn_parser *parser)` - Initialize a JSON parser
- `jsmn_parse(jsmn_parser *parser, const char *js, const size_t len, jsmntok_t *tokens, const unsigned int num_tokens)` - Parse JSON data into tokens

**Token Types**
- `JSMN_OBJECT` - Object token type
- `JSMN_ARRAY` - Array token type
- `JSMN_STRING` - String token type
- `JSMN_PRIMITIVE` - Primitive token type (number, boolean, null)

**Error Codes**
- `JSMN_ERROR_NOMEM` - Not enough tokens were provided
- `JSMN_ERROR_INVAL` - Invalid character inside JSON string
- `JSMN_ERROR_PART` - The string is not a full JSON packet, more bytes expected

### Important Usage Notes

1. **Token-Based Parsing**: jsmn doesn't hold any data but points to token boundaries in the JSON string instead. This allows for zero-copy techniques.

2. **Single-Pass Parsing**: jsmn parses JSON data in a single pass, making it extremely fast and memory efficient.

3. **No Dynamic Memory Allocation**: jsmn doesn't allocate any memory during parsing, making it suitable for resource-constrained environments.

4. **Incremental Parsing**: jsmn supports incremental parsing, allowing you to parse JSON data as it arrives.

5. **Simple Integration**: jsmn is a single-header, header-only library that can be easily integrated into any C project.

6. **Error Handling**: jsmn returns specific error codes for different failure conditions, making error handling straightforward.

7. **Memory Safety**: jsmn is designed to be robust and work fine even with erroneous data.

8. **Portability**: jsmn is highly portable and has been tested on various architectures including x86/amd64, ARM, and AVR.