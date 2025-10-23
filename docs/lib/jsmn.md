# lib/jsmn

## Overview

jsmn (pronounced like 'jasmine') is a minimalistic JSON parser in C. It can be easily integrated into resource-limited or embedded projects. jsmn is designed to be robust (it should work fine even with erroneous data), fast (it should parse data on the fly), portable (no superfluous dependencies or non-standard C extensions), and simple (simple code style, simple algorithm, simple integration into other projects).

jsmn is compatible with C89, has no dependencies (even libc!), is highly portable (tested on x86/amd64, ARM, AVR), has about 200 lines of code, extremely small code footprint, API contains only 2 functions, no dynamic memory allocation, incremental single-pass parsing, and is covered with unit-tests.

## Key Methods/Functions

### Core API
- `jsmn_init(jsmn_parser *parser)` - Initialize a JSON parser
- `jsmn_parse(jsmn_parser *parser, const char *js, const size_t len, jsmntok_t *tokens, const unsigned int num_tokens)` - Parse JSON data into tokens

### Token Types
- `JSMN_UNDEFINED` - Undefined token type
- `JSMN_OBJECT` - Object token type
- `JSMN_ARRAY` - Array token type
- `JSMN_STRING` - String token type
- `JSMN_PRIMITIVE` - Primitive token type (number, boolean, null)

### Token Structure
- `jsmntok_t` - Token structure containing:
  - `type` - Token type (jsmntype_t)
  - `start` - Start position in JSON data string
  - `end` - End position in JSON data string
  - `size` - Number of child (nested) tokens

### Parser Structure
- `jsmn_parser` - Parser structure containing:
  - `pos` - Offset in the JSON string
  - `toknext` - Next token to allocate
  - `toksuper` - Superior token node (parent object or array)

### Error Codes
- `JSMN_ERROR_NOMEM` - Not enough tokens were provided
- `JSMN_ERROR_INVAL` - Invalid character inside JSON string
- `JSMN_ERROR_PART` - The string is not a full JSON packet, more bytes expected

## Important Usage Notes

1. **Token-Based Parsing**: jsmn doesn't hold any data but points to token boundaries in the JSON string instead. This allows for zero-copy techniques.

2. **Single-Pass Parsing**: jsmn parses JSON data in a single pass, making it extremely fast and memory efficient.

3. **No Dynamic Memory Allocation**: jsmn doesn't allocate any memory during parsing, making it suitable for resource-constrained environments.

4. **Incremental Parsing**: jsmn supports incremental parsing, allowing you to parse JSON data as it arrives.

5. **Simple Integration**: jsmn is a single-header, header-only library that can be easily integrated into any C project.

6. **Error Handling**: jsmn returns specific error codes for different failure conditions, making error handling straightforward.

7. **Primitive Type Detection**: Unlike JSON data types, primitive tokens are not divided into numbers, booleans and null, but can be easily distinguished by examining the first character.

8. **String Token Handling**: String tokens point to the first character after the opening quote and the previous symbol before the final quote, simplifying string extraction.

9. **Static Linking**: Define `JSMN_STATIC` to hide all jsmn API symbols by making them static.

10. **Multi-File Usage**: To include `jsmn.h` from multiple C files, define `JSMN_HEADER` macro to avoid duplication of symbols.

11. **Memory Safety**: jsmn is designed to be robust and work fine even with erroneous data.

12. **Portability**: jsmn is highly portable and has been tested on various architectures including x86/amd64, ARM, and AVR.

13. **Performance**: jsmn is designed to be fast, parsing data on the fly without unnecessary overhead.

14. **Compliance**: jsmn follows the JSON specification and handles all valid JSON constructs correctly.