# lib/jansson-e23f558

## Overview

Jansson is a C library for encoding, decoding and manipulating JSON data. Its main features and design principles are:

- Simple and intuitive API and data model
- Comprehensive documentation
- No dependencies on other libraries
- Full Unicode support (UTF-8)
- Extensive test suite

Jansson is licensed under the MIT license.

## Key Methods/Functions

### Construction and Reference Counting
- `json_object()` - Create a new JSON object
- `json_array()` - Create a new JSON array
- `json_string()` - Create a new JSON string
- `json_stringn()` - Create a new JSON string with specified length
- `json_string_nocheck()` - Create a new JSON string without UTF-8 validation
- `json_stringn_nocheck()` - Create a new JSON string with specified length without UTF-8 validation
- `json_integer()` - Create a new JSON integer
- `json_real()` - Create a new JSON real number
- `json_true()` - Create a new JSON true value
- `json_false()` - Create a new JSON false value
- `json_boolean()` - Create a new JSON boolean value
- `json_null()` - Create a new JSON null value
- `json_incref()` - Increment reference count of a JSON value
- `json_decref()` - Decrement reference count of a JSON value
- `json_delete()` - Delete a JSON value (internal use)

### Object Operations
- `json_object_size()` - Get the number of items in a JSON object
- `json_object_get()` - Get a value from a JSON object by key
- `json_object_getn()` - Get a value from a JSON object by key with specified length
- `json_object_set_new()` - Set a value in a JSON object with new value
- `json_object_setn_new()` - Set a value in a JSON object with new value and specified key length
- `json_object_set_new_nocheck()` - Set a value in a JSON object with new value without key validation
- `json_object_setn_new_nocheck()` - Set a value in a JSON object with new value and specified key length without validation
- `json_object_del()` - Delete a value from a JSON object by key
- `json_object_deln()` - Delete a value from a JSON object by key with specified length
- `json_object_clear()` - Clear all items from a JSON object
- `json_object_update()` - Update a JSON object with items from another object
- `json_object_update_existing()` - Update existing items in a JSON object
- `json_object_update_missing()` - Update missing items in a JSON object
- `json_object_update_recursive()` - Recursively update a JSON object
- `json_object_iter()` - Get an iterator for a JSON object
- `json_object_iter_at()` - Get an iterator for a JSON object at a specific key
- `json_object_key_to_iter()` - Convert a key to an iterator
- `json_object_iter_next()` - Get the next iterator in a JSON object
- `json_object_iter_key()` - Get the key from an iterator
- `json_object_iter_key_len()` - Get the key length from an iterator
- `json_object_iter_value()` - Get the value from an iterator
- `json_object_iter_set_new()` - Set a value in a JSON object using an iterator

### Array Operations
- `json_array_size()` - Get the number of items in a JSON array
- `json_array_get()` - Get a value from a JSON array by index
- `json_array_set_new()` - Set a value in a JSON array at a specific index with new value
- `json_array_append_new()` - Append a new value to a JSON array
- `json_array_insert_new()` - Insert a new value at a specific index in a JSON array
- `json_array_remove()` - Remove a value from a JSON array at a specific index
- `json_array_clear()` - Clear all items from a JSON array
- `json_array_extend()` - Extend a JSON array with items from another array

### String Operations
- `json_string_value()` - Get the value of a JSON string
- `json_string_length()` - Get the length of a JSON string
- `json_string_set()` - Set the value of a JSON string
- `json_string_setn()` - Set the value of a JSON string with specified length
- `json_string_set_nocheck()` - Set the value of a JSON string without UTF-8 validation
- `json_string_setn_nocheck()` - Set the value of a JSON string with specified length without UTF-8 validation

### Number Operations
- `json_integer_value()` - Get the value of a JSON integer
- `json_real_value()` - Get the value of a JSON real number
- `json_number_value()` - Get the numeric value of a JSON number
- `json_integer_set()` - Set the value of a JSON integer
- `json_real_set()` - Set the value of a JSON real number

### Packing and Unpacking
- `json_pack()` - Pack arguments into a JSON value using a format string
- `json_pack_ex()` - Pack arguments into a JSON value with error handling
- `json_vpack_ex()` - Pack arguments into a JSON value using a va_list
- `json_unpack()` - Unpack a JSON value into variables using a format string
- `json_unpack_ex()` - Unpack a JSON value with error handling
- `json_vunpack_ex()` - Unpack a JSON value using a va_list

### String Formatting
- `json_sprintf()` - Format arguments into a JSON string
- `json_vsprintf()` - Format arguments into a JSON string using a va_list

### Equality and Copying
- `json_equal()` - Check if two JSON values are equal
- `json_copy()` - Create a shallow copy of a JSON value
- `json_deep_copy()` - Create a deep copy of a JSON value

### Loading and Dumping
- `json_loads()` - Load JSON from a string
- `json_loadb()` - Load JSON from a buffer
- `json_loadf()` - Load JSON from a file
- `json_loadfd()` - Load JSON from a file descriptor
- `json_load_file()` - Load JSON from a file path
- `json_load_callback()` - Load JSON using a callback function
- `json_dumps()` - Dump JSON to a string
- `json_dumpb()` - Dump JSON to a buffer
- `json_dumpf()` - Dump JSON to a file
- `json_dumpfd()` - Dump JSON to a file descriptor
- `json_dump_file()` - Dump JSON to a file path
- `json_dump_callback()` - Dump JSON using a callback function

### Memory Allocation
- `json_set_alloc_funcs()` - Set custom memory allocation functions
- `json_get_alloc_funcs()` - Get the current memory allocation functions

### Version Information
- `jansson_version_str()` - Get the Jansson version string
- `jansson_version_cmp()` - Compare version numbers

## Important Usage Notes

1. **Reference Counting**: Jansson uses reference counting for memory management. Always call `json_decref()` when you're done with a JSON value to prevent memory leaks.

2. **Thread Safety**: Jansson is thread-safe for all read-only functions plus reference counting operations.

3. **UTF-8 Support**: Jansson has full Unicode support with UTF-8 encoding. All strings are assumed to be valid UTF-8.

4. **Error Handling**: Most functions return error codes or NULL on failure. Always check return values.

5. **Memory Management**: Jansson handles its own memory management. Users should not manually free JSON values.

6. **API Simplicity**: Jansson provides a simple and intuitive API for working with JSON data.

7. **No Dependencies**: Jansson has no external dependencies, making it easy to integrate into projects.

8. **Comprehensive Testing**: Jansson has an extensive test suite to ensure reliability.