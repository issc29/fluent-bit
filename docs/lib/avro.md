# Apache Avro

## Overview

Apache Avro is a data serialization system that provides rich data structures and a compact, fast, binary data format. It is designed for efficient serialization of data and includes support for schema evolution.

Avro provides:
- Rich data structures
- A compact, fast, binary data format
- A container file to store persistent data
- Remote procedure call (RPC)

Avro is particularly useful for:
- Data exchange between different systems
- Storing data in a compact binary format
- Schema evolution without breaking compatibility
- Efficient serialization/deserialization operations

## Key Methods/Functions

### Schema Management
- `avro_schema_from_json_literal()` - Parse a schema from a JSON string literal
- `avro_schema_decref()` - Decrement the reference count of a schema
- `avro_schema_record()` - Create a record schema
- `avro_schema_record_field_append()` - Append a field to a record schema
- `avro_schema_string()` - Create a string schema

### Value Operations
- `avro_generic_class_from_schema()` - Create a value class from a schema
- `avro_generic_value_new()` - Create a new generic value
- `avro_value_get_by_name()` - Get a field value by name
- `avro_value_set_long()` - Set a long value
- `avro_value_set_string()` - Set a string value
- `avro_value_set_int()` - Set an integer value
- `avro_value_get_long()` - Get a long value
- `avro_value_get_string()` - Get a string value
- `avro_value_get_int()` - Get an integer value
- `avro_value_decref()` - Decrement the reference count of a value
- `avro_value_iface_decref()` - Decrement the reference count of a value interface

### File Operations
- `avro_file_writer_create_with_codec()` - Create a new file writer with compression
- `avro_file_writer_append_value()` - Append a value to the file
- `avro_file_writer_flush()` - Flush the file writer
- `avro_file_writer_close()` - Close the file writer
- `avro_file_reader()` - Open a file for reading
- `avro_file_reader_read_value()` - Read a value from the file
- `avro_file_reader_close()` - Close the file reader

### Error Handling
- `avro_strerror()` - Get the error message for the last operation

## Important Usage Notes

1. **Schema Evolution**: Avro supports schema evolution, allowing you to add/remove fields while maintaining backward compatibility.
2. **Reference Counting**: Many Avro objects use reference counting. Always decrement references with decref functions to prevent memory leaks.
3. **Binary Format**: Avro uses a compact binary format that is efficient for both storage and transmission.
4. **Schema First**: Avro requires a schema to be defined before serializing data, which helps ensure data consistency.
5. **Value API**: Starting with version 1.6.0, Avro C library has a new API for handling Avro data using the value interface, which is more efficient than the legacy datum API.
6. **Memory Management**: Avro handles its own memory management, but users should properly destroy objects when no longer needed.
7. **Schema Validation**: Data written to an Avro File Object Container is always validated.

## Examples

### Basic Serialization Example
```c
#include <avro.h>

// Define a simple schema
const char PERSON_SCHEMA[] =
"{\"type\":\"record\",\
  \"name\":\"Person\",\
  \"fields\":[\
     {\"name": \"ID\", \"type\": \"long\"},\
     {\"name": \"First\", \"type\": \"string\"},\
     {\"name": \"Last\", \"type\": \"string\"},\
     {\"name": \"Age\", \"type\": \"int\"}]}";

// Parse the schema
avro_schema_t person_schema;
if (avro_schema_from_json_literal(PERSON_SCHEMA, &person_schema)) {
    fprintf(stderr, "Unable to parse person schema\n");
    exit(EXIT_FAILURE);
}

// Create a value and populate it
avro_value_iface_t *person_class = avro_generic_class_from_schema(person_schema);
avro_value_t person;
avro_generic_value_new(person_class, &person);

avro_value_t id_value, first_value, last_value, age_value;
avro_value_get_by_name(&person, "ID", &id_value, NULL);
avro_value_set_long(&id_value, 12345);
avro_value_get_by_name(&person, "First", &first_value, NULL);
avro_value_set_string(&first_value, "John");
avro_value_get_by_name(&person, "Last", &last_value, NULL);
avro_value_set_string(&last_value, "Doe");
avro_value_get_by_name(&person, "Age", &age_value, NULL);
avro_value_set_int(&age_value, 30);

// Write to file
avro_file_writer_t db;
if (avro_file_writer_create_with_codec("person.avro", person_schema, &db, "null", 0)) {
    fprintf(stderr, "Error creating file\n");
    exit(EXIT_FAILURE);
}

if (avro_file_writer_append_value(db, &person)) {
    fprintf(stderr, "Error writing person\n");
    exit(EXIT_FAILURE);
}

avro_file_writer_close(db);

// Clean up
avro_value_decref(&person);
avro_value_iface_decref(person_class);
avro_schema_decref(person_schema);
```