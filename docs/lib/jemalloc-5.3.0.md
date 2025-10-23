# lib/jemalloc-5.3.0

## Overview

jemalloc is a general purpose malloc(3) implementation that emphasizes fragmentation avoidance and scalable concurrency support. jemalloc first came into use as the FreeBSD libc allocator in 2005, and since then it has found its way into numerous applications that rely on its predictable behavior. In 2010 jemalloc development efforts broadened to include developer support features such as heap profiling and extensive monitoring/tuning hooks.

Modern jemalloc releases continue to be integrated back into FreeBSD, and therefore versatility remains critical. Ongoing development efforts trend toward making jemalloc among the best allocators for a broad range of demanding applications, and eliminating/mitigating weaknesses that have practical repercussions for real world applications.

## Key Methods/Functions

### Standard Memory Allocation
- `malloc(size_t size)` - Allocate memory block
- `calloc(size_t num, size_t size)` - Allocate and zero-initialize memory block
- `realloc(void *ptr, size_t size)` - Reallocate memory block
- `free(void *ptr)` - Deallocate memory block
- `posix_memalign(void **memptr, size_t alignment, size_t size)` - Allocate aligned memory block
- `aligned_alloc(size_t alignment, size_t size)` - Allocate aligned memory block

### Extended Allocation
- `mallocx(size_t size, int flags)` - Extended memory allocation
- `rallocx(void *ptr, size_t size, int flags)` - Extended memory reallocation
- `xallocx(void *ptr, size_t size, size_t extra, int flags)` - Extend memory allocation
- `sallocx(const void *ptr, int flags)` - Get allocation size
- `dallocx(void *ptr, int flags)` - Extended memory deallocation
- `sdallocx(void *ptr, size_t size, int flags)` - Size-aware deallocation

### Arena Management
- `nallocx(size_t size, int flags)` - Non-allocating size computation
- `mallctl(const char *name, void *oldp, size_t *oldlenp, void *newp, size_t newlen)` - Control interface
- `mallctlnametomib(const char *name, size_t *mibp, size_t *miblenp)` - Name to MIB conversion
- `mallctlbymib(const size_t *mib, size_t miblen, void *oldp, size_t *oldlenp, void *newp, size_t newlen)` - MIB-based control interface

### Thread-Specific Operations
- `malloc_usable_size(const void *ptr)` - Get usable size of allocation
- `malloc_stats_print(void (*write_cb)(void *, const char *), void *cbopaque, const char *opts)` - Print statistics

### Arena Creation and Management
- `arenas.create(unsigned *arena_ind)` - Create new arena
- `arenas.destroy(unsigned arena_ind)` - Destroy arena
- `arenas.i.initialized` - Check if arena is initialized
- `arenas.i.decay` - Trigger decay for arena
- `arenas.i.purge` - Purge arena
- `arenas.i.reset` - Reset arena
- `arenas.i.destroy` - Destroy arena

### Extent Hooks
- `extent_hooks_t` - Extent hook function table
- `extent_alloc_t` - Extent allocation hook
- `extent_dalloc_t` - Extent deallocation hook
- `extent_destroy_t` - Extent destruction hook
- `extent_commit_t` - Extent commit hook
- `extent_decommit_t` - Extent decommit hook
- `extent_purge_t` - Extent purge hook
- `extent_split_t` - Extent split hook
- `extent_merge_t` - Extent merge hook

### Thread Arena Management
- `thread.arena` - Get/set current thread's arena
- `thread.allocated` - Get thread's allocated bytes
- `thread.deallocated` - Get thread's deallocated bytes

### Configuration Options
- `background_thread` - Enable background purging threads
- `metadata_thp` - Transparent huge page setting for metadata
- `dirty_decay_ms` - Decay time for dirty pages
- `muzzy_decay_ms` - Decay time for muzzy pages
- `narenas` - Number of arenas
- `percpu_arena` - Per-CPU arena mode

## Important Usage Notes

1. **Drop-in Replacement**: jemalloc is designed to be a drop-in replacement for the standard C library's malloc implementation.

2. **Thread Safety**: jemalloc is fully thread-safe and designed for concurrent access from multiple threads.

3. **Scalability**: jemalloc uses multiple arenas to reduce lock contention and improve scalability on multi-core systems.

4. **Memory Efficiency**: jemalloc focuses on minimizing memory fragmentation through its sophisticated allocation strategies.

5. **Tuning Options**: jemalloc provides extensive runtime tuning options through the mallctl interface and environment variables.

6. **Profiling Support**: jemalloc includes built-in heap profiling capabilities for memory usage analysis.

7. **Background Threads**: jemalloc can use background threads for memory purging operations to improve application responsiveness.

8. **Huge Page Support**: jemalloc can utilize transparent huge pages for better performance on systems with large memory footprints.

9. **Custom Extents**: Applications can provide custom extent hooks for specialized memory management requirements.

10. **Statistics Monitoring**: jemalloc provides detailed statistics about memory usage and allocation patterns.

11. **Decay Mechanisms**: jemalloc implements sophisticated decay mechanisms for returning unused memory to the operating system.

12. **Per-CPU Arenas**: jemalloc can bind threads to arenas based on CPU affinity for improved locality.