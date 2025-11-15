# File Documentation: parser.cpp

## Metadata

- **File Path**: `src/parser.cpp`
- **File Name**: `parser.cpp`
- **Language**: C++
- **Lines of Code**: 183
- **Characters**: 4510
- **Words**: 472
- **Last Modified**: 2025-11-15T20:07:20.480877

## Original Source Code

```cpp
#pragma once

#include <string>
#include <cstdint>
#include <vector>
#include <memory>
#include <iostream>
#include <filesystem>
#include "../include/message.h"
#include <sys/mman.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdexcept>
#include <cstring>

class ParserException : public std::runtime_error {
public:
  explicit ParserException(const std::string &msg) :
    std::runtime_error(msg) {
  }
};

class Parser {
private:
  std::string file_path_;
  char *mapped_file_;
  size_t file_size_;

  void parse_mapped_data() {
    char *current = mapped_file_;
    char *end = mapped_file_ + file_size_;

    for (int i = 0; i < 2 && current < end; ++i) {
      current = static_cast<char *>(memchr(current, '\n', end - current));
      if (current)
        ++current;
      else
        throw ParserException("invalid file format: missing header");
    }

    while (current < end) {
      char *line_end = static_cast<char *>(
        memchr(current, '\n', end - current));
      if (!line_end)
        line_end = end;

      parse_line(current, line_end);
      current = line_end + 1;
    }
  }

  void parse_line(const char *start, const char *end) {
    uint64_t ts_event, order_id;
    int32_t price;
    uint32_t size;
    char action, side;

    const char *token_start = start;
    const char *token_end;

    token_end = strchr(token_start, ',');
    ts_event = strtoull(token_start, nullptr, 10);
    token_start = token_end + 1;

    action = *token_start;
    token_start = strchr(token_start, ',') + 1;

    side = *token_start;
    token_start = strchr(token_start, ',') + 1;

    token_end = strchr(token_start, ',');
    price = strtol(token_start, nullptr, 10);
    token_start = token_end + 1;

    token_end = strchr(token_start, ',');
    size = strtoul(token_start, nullptr, 10);
    token_start = token_end + 1;

    order_id = strtoull(token_start, nullptr, 10);

    bool bid_or_ask = (side == 'B');
    message_stream_.emplace_back(order_id, ts_event, size, price, action,
                                 bid_or_ask);
  }

  void cleanup() {
    if (mapped_file_) {
      munmap(mapped_file_, file_size_);
      mapped_file_ = nullptr;
    }
  }

  [[nodiscard]] bool check_file_format() const { return true; }

  [[nodiscard]] bool verify_message_consistency() const { return true; }

public:
  std::vector<book_message> message_stream_;

  explicit Parser(const std::string &file_path) :
    file_path_(file_path), mapped_file_(nullptr), file_size_(0) {

    if (!std::filesystem::exists(file_path)) {
      throw ParserException("file does not exist: " + file_path);
    }

    message_stream_.reserve(9000000);
  }

  ~Parser() { cleanup(); }

  Parser(Parser &&other) noexcept :
    file_path_(std::move(other.file_path_))
    , mapped_file_(other.mapped_file_)
    , file_size_(other.file_size_)
    , message_stream_(std::move(other.message_stream_)) {

    other.mapped_file_ = nullptr;
    other.file_size_ = 0;
  }

  Parser &operator=(Parser &&other) noexcept {
    if (this != &other) {
      cleanup();

      file_path_ = std::move(other.file_path_);
      mapped_file_ = other.mapped_file_;
      file_size_ = other.file_size_;
      message_stream_ = std::move(other.message_stream_);

      other.mapped_file_ = nullptr;
      other.file_size_ = 0;
    }
    return *this;
  }

  Parser(const Parser &) = delete;
  Parser &operator=(const Parser &) = delete;

  void parse() {
    std::cout << "parsing messages" << std::endl;

    int fd = open(file_path_.c_str(), O_RDONLY);
    if (fd == -1) {
      throw ParserException("failed to open file: " + file_path_);
    }

    struct stat sb;
    if (fstat(fd, &sb) == -1) {
      close(fd);
      throw ParserException("failed to get file stats");
    }

    file_size_ = sb.st_size;

    mapped_file_ = static_cast<char *>(mmap(nullptr, file_size_, PROT_READ,
                                            MAP_PRIVATE, fd, 0));
    close(fd);

    if (mapped_file_ == MAP_FAILED) {
      mapped_file_ = nullptr;
      throw ParserException("failed to memory map file");
    }

    try {
      parse_mapped_data();
    } catch (const std::exception &e) {
      cleanup();
      throw;
    }

    std::cout << "finished parsing" << std::endl;
  }

  bool validate_file() const {
    return check_file_format() && verify_message_consistency();
  }

  const std::string &get_file_path() const { return file_path_; }

  size_t get_message_count() const { return message_stream_.size(); }
};
```

## High-Level Overview

This is a C++ implementation file containing function definitions and business logic.

**Functions Implemented:** 6 function(s)
- `parse()`
- `validate_file()`
- `get_message_count()`
- `cleanup()`
- `parse_mapped_data()`
- `parse_line()`



## Detailed Code Walkthrough

### Include Directives

The file includes the following headers:

- `string`: Standard library header
- `cstdint`: Project-specific header
- `vector`: Standard library header
- `memory`: Project-specific header
- `iostream`: Standard library header
- `filesystem`: Project-specific header
- `../include/message.h`: Project-specific header
- `sys/mman.h`: Project-specific header
- `sys/stat.h`: Project-specific header
- `fcntl.h`: Project-specific header
- `unistd.h`: Project-specific header
- `stdexcept`: Standard library header
- `cstring`: Project-specific header

### Class: `ParserException`

This class provides functionality related to parserexception.

**Member Functions:**
- `runtime_error()`
- `ParserException()`

### Class: `Parser`

This class provides functionality related to parser.

**Member Functions:**
- `strchr()`
- `emplace_back()`
- `ParserException()`
- `parse_mapped_data()`
- `strtoul()`
- `if()`
- `for()`
- `parse_line()`
- `strtol()`
- `memchr()`
- `strtoull()`
- `while()`

### Standalone Functions

#### `parse_mapped_data()`

Function that performs operations related to parse_mapped_data.

#### `ParserException("invalid file format: missing header")`

Function that performs operations related to ParserException.

#### `parse_line(const char *start, const char *end)`

Function that performs operations related to parse_line.

#### `cleanup()`

Function that performs operations related to cleanup.

#### `ParserException("file does not exist: " + file_path)`

Function that performs operations related to ParserException.

#### `parse()`

Function that performs operations related to parse.

#### `ParserException("failed to open file: " + file_path_)`

Function that performs operations related to ParserException.

#### `ParserException("failed to get file stats")`

Function that performs operations related to ParserException.

#### `ParserException("failed to memory map file")`

Function that performs operations related to ParserException.

#### `validate_file()`

Function that performs operations related to validate_file.



## Usage Examples

See the source code implementation for usage details.


## Performance & Security Notes

**Performance**: Uses move semantics for efficient resource management.

**Security**: ⚠️ Contains references to sensitive data. Ensure proper handling and storage.



## Related Files

**Included Headers:**
- [`string`](../string)
- [`cstdint`](../cstdint)
- [`vector`](../vector)
- [`memory`](../memory)
- [`iostream`](../iostream)
- [`filesystem`](../filesystem)
- `../include/message.h`
- `sys/mman.h`
- `sys/stat.h`
- [`fcntl.h`](../fcntl.h)
- [`unistd.h`](../unistd.h)
- `stdexcept`
- [`cstring`](../cstring)

**Header File**: [`parser.h`](../parser.h)



## Testing

Uses Catch2 testing framework.

