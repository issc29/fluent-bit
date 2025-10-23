# CProfiles

## Overview

CProfiles is a simple API to create and manage profiles for monitoring and observability purposes. The internal data structure is based on the OpenTelemetry Profiles schema (v1/development):

- https://github.com/open-telemetry/opentelemetry-proto/blob/main/opentelemetry/proto/profiles/v1development/profiles.proto

The library provides a C implementation for working with profile data, including samples, locations, functions, and mappings. It supports encoding and decoding in various formats for interoperability with other profiling systems.

## Key Methods/Functions

### Library Initialization

- `cprof_create()` - Create a new CProfiles context
- `cprof_destroy()` - Destroy a CProfiles context and release all resources
- `cprof_version()` - Get the library version string

### Profile Operations

- `cprof_profile_create()` - Create a new profile
- `cprof_profile_destroy()` - Destroy a profile
- `cprof_profile_add_location_index()` - Add a location index to a profile
- `cprof_profile_string_add()` - Add a string to the profile's string table
- `cprof_profile_add_comment()` - Add a comment to a profile

### Resource Operations

- `cprof_resource_create()` - Create a new resource
- `cprof_resource_destroy()` - Destroy a resource
- `cprof_resource_profiles_add()` - Add resource profiles to a context

### Instrumentation Scope Operations

- `cprof_instrumentation_scope_create()` - Create a new instrumentation scope
- `cprof_instrumentation_scope_destroy()` - Destroy an instrumentation scope

### Scope Profiles Operations

- `cprof_scope_profiles_create()` - Create new scope profiles
- `cprof_scope_profiles_destroy()` - Destroy scope profiles

### Resource Profiles Operations

- `cprof_resource_profiles_create()` - Create new resource profiles
- `cprof_resource_profiles_destroy()` - Destroy resource profiles

### Function Operations

- `cprof_function_create()` - Create a new function
- `cprof_function_destroy()` - Destroy a function

### Link Operations

- `cprof_link_create()` - Create a new link
- `cprof_link_destroy()` - Destroy a link

### Sample Operations

- `cprof_sample_create()` - Create a new sample
- `cprof_sample_destroy()` - Destroy a sample
- `cprof_sample_destroy_all()` - Destroy all samples in a profile
- `cprof_sample_add_timestamp()` - Add a timestamp to a sample
- `cprof_sample_add_value()` - Add a value to a sample
- `cprof_sample_add_location_index()` - Add a location index to a sample
- `cprof_sample_add_attribute()` - Add an attribute to a sample

### Sample Type Operations

- `cprof_sample_type_create()` - Create a new sample type
- `cprof_sample_type_str_create()` - Create a new sample type with string values
- `cprof_sample_type_destroy()` - Destroy a sample type
- `cprof_sample_type_destroy_all()` - Destroy all sample types in a profile

### Attribute Unit Operations

- `cprof_attribute_unit_create()` - Create a new attribute unit
- `cprof_attribute_unit_destroy()` - Destroy an attribute unit

### Mapping Operations

- `cprof_mapping_create()` - Create a new mapping
- `cprof_mapping_destroy()` - Destroy a mapping
- `cprof_mapping_add_attribute()` - Add an attribute to a mapping

### Line Operations

- `cprof_line_create()` - Create a new line
- `cprof_line_destroy()` - Destroy a line

### Location Operations

- `cprof_location_create()` - Create a new location
- `cprof_location_destroy()` - Destroy a location
- `cprof_location_add_attribute()` - Add an attribute to a location

### Encoding/Decoding Functions

- `cprof_encode_msgpack()` - Encode profiles in MessagePack format
- `cprof_encode_opentelemetry()` - Encode profiles in OpenTelemetry format
- `cprof_encode_text()` - Encode profiles in text format
- `cprof_decode_msgpack()` - Decode profiles from MessagePack format
- `cprof_decode_opentelemetry()` - Decode profiles from OpenTelemetry format

## Important Usage Notes

1. **OpenTelemetry Compatibility**: CProfiles is designed to be compatible with the OpenTelemetry Profiles schema, ensuring interoperability with the broader observability ecosystem.

2. **Hierarchical Structure**: Profiles follow a hierarchical structure with resources, scopes, and individual profiles, allowing for rich metadata organization.

3. **Memory Management**: The library handles its own memory management, but users should properly destroy contexts and profile objects when no longer needed.

4. **String Table**: Profiles use a string table to efficiently store and reference string values, reducing memory usage for repeated strings.

5. **Location Tracking**: The library provides comprehensive location tracking with support for functions, mappings, and line information for detailed profiling data.

6. **Sample Data**: Profiles contain sample data with values, timestamps, and attributes for comprehensive performance analysis.

7. **Thread Safety**: The library is designed to be thread-safe for concurrent profile operations, though applications should be aware of potential contention.

8. **Encoding Formats**: Multiple encoding formats are supported for interoperability with different profiling tools and systems.

## Example Usage

```c
// Create a context
struct cprof *cprof = cprof_create();

// Create a resource
struct cfl_kvlist *resource_attrs = cfl_kvlist_create();
cfl_kvlist_insert(resource_attrs, "service.name", "my-service");
struct cprof_resource *resource = cprof_resource_create(resource_attrs);

// Create resource profiles
struct cprof_resource_profiles *resource_profiles = cprof_resource_profiles_create("https://opentelemetry.io/schemas/1.0.0");
resource_profiles->resource = resource;

// Create scope profiles
struct cprof_scope_profiles *scope_profiles = cprof_scope_profiles_create(resource_profiles, "https://opentelemetry.io/schemas/1.0.0");

// Create a profile
struct cprof_profile *profile = cprof_profile_create();

// Add sample type
struct cprof_value_type *sample_type = cprof_sample_type_str_create(profile, "cpu", "nanoseconds", CPROF_AGGREGATION_TEMPORALITY_CUMULATIVE);

// Add a sample
struct cprof_sample *sample = cprof_sample_create(profile);
cprof_sample_add_value(sample, 1000000); // 1ms

// Add to profile
cfl_list_add(&sample->_head, &profile->samples);

// Add to scope profiles
cfl_list_add(&profile->_head, &scope_profiles->profiles);

// Add to resource profiles
cfl_list_add(&scope_profiles->_head, &resource_profiles->scope_profiles);

// Add to context
cfl_list_add(&resource_profiles->_head, &cprof->profiles);

// Encode in text format
char *output;
size_t output_size;
int ret = cprof_encode_text(cprof, &output, &output_size);

// Clean up
cprof_destroy(cprof);
```