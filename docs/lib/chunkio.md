# Chunk I/O

## Overview

Chunk I/O is a library to manage chunks of data in the file system and load in memory upon demand. It's designed to support:

- Fixed path in the file system to organize data (root_path)
- Streams: categorize data into streams
- Multiple data files per stream
- Data file or chunks composed by:
  - Optional CRC32 content checksum
  - Metadata (optional, up to 65535 bytes)
  - User data

The library uses a root path to store content, where different streams can be defined to store data files called chunks. Each chunk file has a specific layout with header, optional CRC32 checksum, metadata, and user data.

## Key Methods/Functions

### Library Initialization and Management

- `cio_options_init()` - Initialize the options structure for creating a context
- `cio_create()` - Create a new Chunk I/O context
- `cio_destroy()` - Destroy a Chunk I/O context and release all resources
- `cio_load()` - Load chunks from the file system
- `cio_qsort()` - Sort chunks in a context using a custom comparison function

### Logging Configuration

- `cio_set_log_callback()` - Set a custom logging callback function
- `cio_set_log_level()` - Set the logging level (ERROR, WARN, INFO, DEBUG, TRACE)

### Context Configuration

- `cio_set_max_chunks_up()` - Set the maximum number of chunks to keep in memory
- `cio_set_realloc_size_hint()` - Set the size hint for memory reallocation
- `cio_enable_file_trimming()` - Enable automatic file trimming to reduce disk usage
- `cio_disable_file_trimming()` - Disable automatic file trimming

### Stream Operations

- `cio_stream_create()` - Create a new stream in a context
- `cio_stream_destroy()` - Destroy a stream and all its chunks
- `cio_stream_get()` - Get a stream by name

### Chunk Operations

- `cio_chunk_open()` - Open or create a chunk
- `cio_chunk_close()` - Close a chunk and sync to disk
- `cio_chunk_write()` - Write data to a chunk
- `cio_chunk_read()` - Read data from a chunk
- `cio_chunk_sync()` - Force synchronization of a chunk to disk
- `cio_chunk_get_content()` - Get the content of a chunk
- `cio_chunk_get_content_size()` - Get the size of chunk content
- `cio_chunk_is_up()` - Check if a chunk is loaded in memory

### Metadata Operations

- `cio_meta_write()` - Write metadata to a chunk
- `cio_meta_read()` - Read metadata from a chunk
- `cio_meta_cmp()` - Compare metadata with a buffer
- `cio_meta_size()` - Get the size of metadata in a chunk

## Important Usage Notes

1. **File System Structure**: Chunk I/O organizes data in a hierarchical structure with a root path containing streams, which in turn contain chunks.

2. **Memory Management**: The library uses memory mapping (mmap) for efficient I/O operations. Chunks can be loaded into memory on demand to optimize memory usage.

3. **Checkpointing**: Chunk I/O supports checkpointing mechanisms through metadata, allowing applications to track state and recover from interruptions.

4. **Data Integrity**: Optional CRC32 checksums can be enabled to verify data integrity during read operations.

5. **Synchronization Modes**: Two synchronization modes are available:
   - Normal (MAP_ASYNC): Let the kernel decide when to flush memory pages to disk
   - Full (MAP_SYNC): Force immediate synchronization to ensure data safety

6. **Performance Considerations**: The library is designed for high-performance I/O operations, with configurable limits on the number of chunks kept in memory to balance between performance and resource usage.

7. **Error Handling**: The library provides detailed error codes for different failure scenarios, including corrupted chunks and retryable operations.

8. **Thread Safety**: Chunk I/O is designed to be thread-safe for concurrent operations on different chunks, though concurrent operations on the same chunk should be synchronized by the application.

## Example Usage

```c
// Initialize options
struct cio_options opts = {0};
cio_options_init(&opts);
opts.flags = CIO_CHECKSUM;
opts.root_path = "/tmp/chunkio";
opts.log_level = CIO_LOG_INFO;

// Create context
struct cio_ctx *ctx = cio_create(&opts);

// Create a stream
struct cio_stream *stream = cio_stream_create(ctx, "my_stream", CIO_STORE_FS);

// Open a chunk
struct cio_chunk *chunk = cio_chunk_open(stream, "my_chunk", CIO_OPEN_RW, 0, 0);

// Write data to chunk
const char *data = "Hello, Chunk I/O!";
cio_chunk_write(chunk, data, strlen(data));

// Sync to disk
cio_chunk_sync(chunk);

// Clean up
cio_chunk_close(chunk, NULL);
cio_stream_destroy(stream);
cio_destroy(ctx);
```