# CFL (Common Fluent Library)

## Overview

CFL is a tiny library that provides interfaces for data structures, originally created to satisfy the needs of Fluent Bit and other libraries used internally like CMetrics and CTraces projects. It offers a collection of lightweight, efficient data structures and utilities commonly needed in C applications.

CFL provides implementations for:
- String manipulation (Simple Dynamic Strings)
- Linked lists
- Key-value pairs
- Arrays
- Variant types
- Time utilities
- Hash functions

The library is designed to be minimal and focused, providing only what's needed for efficient data handling in performance-critical applications.

Note: The name doesn't mean anything specific, you can call it `c:\ floppy` if you want.

## Key Methods/Functions

### Core Library
- `cfl_init()` - Initialize the CFL library
- `cfl_version()` - Get the CFL version string

### String Operations (cfl_sds)
- `cfl_sds_create()` - Create a new SDS string
- `cfl_sds_create_len()` - Create a new SDS string with specified length
- `cfl_sds_create_size()` - Create a new SDS string with specified size
- `cfl_sds_destroy()` - Destroy an SDS string
- `cfl_sds_cat()` - Concatenate a string to an SDS
- `cfl_sds_cat_safe()` - Safely concatenate a string to an SDS
- `cfl_sds_printf()` - Format and append to an SDS
- `cfl_sds_len()` - Get the length of an SDS
- `cfl_sds_alloc()` - Get the allocated size of an SDS
- `cfl_sds_avail()` - Get the available space in an SDS
- `cfl_sds_increase()` - Increase the size of an SDS
- `cfl_sds_set_len()` - Set the length of an SDS

### Linked List Operations (cfl_list)
- `cfl_list_init()` - Initialize a list
- `cfl_list_add()` - Add an item to the beginning of a list
- `cfl_list_append()` - Add an item to the end of a list
- `cfl_list_prepend()` - Add an item to the beginning of a list
- `cfl_list_del()` - Remove an item from a list
- `cfl_list_is_empty()` - Check if a list is empty
- `cfl_list_size()` - Get the size of a list
- `cfl_list_entry_init()` - Initialize a list entry
- `cfl_list_entry_is_orphan()` - Check if a list entry is orphaned
- `cfl_list_cat()` - Concatenate two lists

### Key-Value Operations (cfl_kv)
- `cfl_kv_create()` - Create a new key-value pair
- `cfl_kv_destroy()` - Destroy a key-value pair
- `cfl_kv_item_create()` - Create a new key-value item
- `cfl_kv_item_destroy()` - Destroy a key-value item

### Array Operations (cfl_array)
- `cfl_array_create()` - Create a new array
- `cfl_array_destroy()` - Destroy an array
- `cfl_array_append()` - Append an element to an array
- `cfl_array_get()` - Get an element from an array
- `cfl_array_size()` - Get the size of an array
- `cfl_array_is_empty()` - Check if an array is empty

### Hash Operations (cfl_hash)
- `cfl_hash_64bits()` - Compute a 64-bit hash
- `cfl_hash_64bits_reset()` - Reset a hash state
- `cfl_hash_64bits_update()` - Update a hash state
- `cfl_hash_64bits_digest()` - Get the digest of a hash state
- `cfl_hash_128bits()` - Compute a 128-bit hash

### Time Operations (cfl_time)
- `cfl_time_now()` - Get the current time in nanoseconds

### Variant Operations (cfl_variant)
- `cfl_variant_create()` - Create a new variant
- `cfl_variant_destroy()` - Destroy a variant
- `cfl_variant_set_int64()` - Set a variant to an int64 value
- `cfl_variant_set_uint64()` - Set a variant to a uint64 value
- `cfl_variant_set_double()` - Set a variant to a double value
- `cfl_variant_set_bool()` - Set a variant to a boolean value
- `cfl_variant_set_string()` - Set a variant to a string value
- `cfl_variant_get_int64()` - Get an int64 value from a variant
- `cfl_variant_get_uint64()` - Get a uint64 value from a variant
- `cfl_variant_get_double()` - Get a double value from a variant
- `cfl_variant_get_bool()` - Get a boolean value from a variant
- `cfl_variant_get_string()` - Get a string value from a variant

## Important Usage Notes

1. **Minimal Design**: CFL is intentionally minimal, focusing only on essential data structures and utilities.
2. **Performance Focus**: All implementations are designed for efficiency in performance-critical applications.
3. **Memory Management**: CFL handles its own memory management, but users should properly destroy objects when no longer needed.
4. **String Safety**: The SDS (Simple Dynamic String) implementation is designed to be safe and efficient for string operations.
5. **List Efficiency**: The linked list implementation is highly optimized for common operations like insertion and deletion.
6. **Hash Quality**: The hash functions use high-quality algorithms (XXH3) for good distribution properties.
7. **Thread Safety**: CFL is designed to be thread-safe for most operations, making it suitable for concurrent applications.

## Examples

### Linked List Example
```c
#include <cfl/cfl.h>
#include <stdio.h>
#include <stdlib.h>

struct my_item {
    struct cfl_list _head;
    int value;
    char *name;
};

int main()
{
    struct cfl_list list;
    struct cfl_list *tmp;
    struct my_item *item;
    
    // Initialize the list
    cfl_list_init(&list);
    
    // Add some items
    for (int i = 0; i < 5; i++) {
        item = malloc(sizeof(struct my_item));
        item->value = i * 10;
        item->name = malloc(20);
        snprintf(item->name, 20, "Item_%d", i);
        
        cfl_list_add(&item->_head, &list);
    }
    
    // Print all items
    printf("List contents:\n");
    cfl_list_foreach(tmp, &list) {
        item = cfl_list_entry(tmp, struct my_item, _head);
        printf("- %s: %d\n", item->name, item->value);
    }
    
    // Clean up
    struct cfl_list *tmp_safe, *head;
    cfl_list_foreach_safe(tmp, tmp_safe, &list) {
        item = cfl_list_entry(tmp, struct my_item, _head);
        free(item->name);
        free(item);
    }
    
    return 0;
}
```

### String Operations Example
```c
#include <cfl/cfl.h>
#include <stdio.h>

int main()
{
    cfl_sds_t str;
    
    // Create a string
    str = cfl_sds_create("Hello ");
    
    // Append to the string
    str = cfl_sds_cat(str, "World!", 6);
    
    // Print the string
    printf("String: %s\n", str);
    printf("Length: %zu\n", cfl_sds_len(str));
    
    // Format and append
    str = cfl_sds_printf(&str, " Length is %zu", cfl_sds_len(str));
    printf("Updated string: %s\n", str);
    
    // Clean up
    cfl_sds_destroy(str);
    
    return 0;
}
```