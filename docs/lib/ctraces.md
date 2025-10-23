# lib/ctraces

## Overview

CTraces is a tiny library to create and maintain Traces contexts and provide utilities for data manipulation, including encoding/decoding for compatibility with OpenTelemetry and other formats. It is a core library for Fluent Bit, serving as an agent and aggregator for Observability data.

The library provides a comprehensive API for creating and managing distributed tracing data, with support for spans, resources, scopes, attributes, events, and links. It also includes encoding/decoding capabilities for various formats including MessagePack, OpenTelemetry Protocol (OTLP), and human-readable text.

## Key Methods/Functions

### Context Management
- `ctr_create()` - Create a new CTrace context
- `ctr_destroy()` - Destroy a CTrace context and free associated resources
- `ctr_opts_init()` - Initialize CTrace options
- `ctr_opts_set()` - Set options for CTrace context
- `ctr_opts_exit()` - Clean up CTrace options

### Resource Management
- `ctr_resource_span_create()` - Create a new resource span
- `ctr_resource_span_set_schema_url()` - Set schema URL for a resource span
- `ctr_resource_span_get_resource()` - Get the resource associated with a resource span
- `ctr_resource_set_dropped_attr_count()` - Set dropped attribute count for a resource

### Scope Management
- `ctr_scope_span_create()` - Create a new scope span
- `ctr_scope_span_set_schema_url()` - Set schema URL for a scope span
- `ctr_scope_span_set_instrumentation_scope()` - Set instrumentation scope for a scope span
- `ctr_instrumentation_scope_create()` - Create a new instrumentation scope

### Span Management
- `ctr_span_create()` - Create a new span
- `ctr_span_set_span_id_with_cid()` - Set span ID for a span using a CTrace ID
- `ctr_span_set_trace_id_with_cid()` - Set trace ID for a span using a CTrace ID
- `ctr_span_set_parent_span_id_with_cid()` - Set parent span ID for a span using a CTrace ID
- `ctr_span_kind_set()` - Set the kind of a span (client, server, etc.)

### Attribute Management
- `ctr_span_set_attribute_string()` - Set a string attribute on a span
- `ctr_span_set_attribute_int64()` - Set an integer attribute on a span
- `ctr_span_set_attribute_double()` - Set a double attribute on a span
- `ctr_span_set_attribute_bool()` - Set a boolean attribute on a span
- `ctr_span_set_attribute_array()` - Set an array attribute on a span
- `ctr_span_set_attribute_kvlist()` - Set a key-value list attribute on a span

### Event Management
- `ctr_span_event_add()` - Add an event to a span
- `ctr_span_event_set_attribute_string()` - Set a string attribute on a span event
- `ctr_span_event_set_attribute_int64()` - Set an integer attribute on a span event
- `ctr_span_event_set_attribute_double()` - Set a double attribute on a span event
- `ctr_span_event_set_attribute_bool()` - Set a boolean attribute on a span event

### Link Management
- `ctr_link_create_with_cid()` - Create a new link using CTrace IDs
- `ctr_link_set_trace_state()` - Set trace state for a link
- `ctr_link_set_dropped_attr_count()` - Set dropped attribute count for a link

### ID Management
- `ctr_id_create_random()` - Create a random CTrace ID
- `ctr_id_destroy()` - Destroy a CTrace ID and free associated resources

### Encoding Functions
- `ctr_encode_text_create()` - Encode trace data as human-readable text
- `ctr_encode_text_destroy()` - Free resources allocated for text encoding
- `ctr_encode_msgpack_create()` - Encode trace data in MessagePack format
- `ctr_encode_msgpack_destroy()` - Free resources allocated for MessagePack encoding
- `ctr_encode_opentelemetry_create()` - Encode trace data in OpenTelemetry format
- `ctr_encode_opentelemetry_destroy()` - Free resources allocated for OpenTelemetry encoding

### Decoding Functions
- `ctr_decode_opentelemetry_create()` - Decode trace data from OpenTelemetry format
- `ctr_decode_opentelemetry_destroy()` - Free resources allocated for OpenTelemetry decoding
- `ctr_decode_msgpack_create()` - Decode trace data from MessagePack format
- `ctr_decode_msgpack_destroy()` - Free resources allocated for MessagePack decoding

## Important Usage Notes

1. **Context Lifecycle**: Always create a CTrace context with `ctr_create()` and destroy it with `ctr_destroy()` when done to prevent memory leaks.
2. **ID Management**: CTrace IDs should be created with `ctr_id_create_random()` and destroyed with `ctr_id_destroy()` when no longer needed.
3. **Resource Spans**: Resource spans are the top-level containers that hold scopes and spans. Each trace should have at least one resource span.
4. **Scope Spans**: Scope spans group spans by instrumentation library. They provide context about which library generated the spans.
5. **Span Hierarchy**: Spans can have parent-child relationships. Child spans inherit the trace ID from their parent but have their own span ID.
6. **Attributes**: Attributes can be added to spans and events to provide additional context. Supported types include strings, integers, doubles, booleans, arrays, and key-value lists.
7. **Events**: Events can be added to spans to mark significant points in the span's execution timeline.
8. **Links**: Links can connect spans that are causally related but not in a parent-child relationship.
9. **Encoding/Decoding**: The library provides multiple encoding formats for interoperability with different systems and protocols.
10. **Memory Management**: The library handles its own memory management, but users should properly destroy contexts, IDs, and encoded data when no longer needed.
11. **Thread Safety**: CTraces is designed to be thread-safe for most operations, making it suitable for concurrent applications.
12. **OpenTelemetry Compatibility**: The library is designed for compatibility with OpenTelemetry specifications and protocols.