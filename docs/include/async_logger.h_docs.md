# File Documentation: async_logger.h

## Metadata

- **File Path**: `include/async_logger.h`
- **File Name**: `async_logger.h`
- **Language**: C++ Header
- **Lines of Code**: 65
- **Characters**: 1639
- **Words**: 144
- **Last Modified**: 2025-11-15T20:07:20.473876

## Original Source Code

```h
#pragma once
#include <string>
#include <thread>
#include <atomic>
#include <sys/mman.h>
#include <fcntl.h>
#include "db_connection.h"
#include "lock_free_queue.h"

class AsyncLogger {
private:
  struct LogEntry {
    std::string timestamp;
    int32_t bid;
    int32_t ask;
    int position;
    int trade_count;
    float pnl;
    std::string instrument_id;
  };

  std::thread console_thread_;
  std::thread csv_thread_;
  std::atomic<bool> running_{true};

  Connection *db_connection_;
  std::string instrument_id_;

  int log_fd_;
  char *log_buffer_;
  size_t buffer_size_;
  size_t buffer_offset_;
  static constexpr size_t DEFAULT_BUFFER_SIZE = 10 * 1024 * 1024;

  LockFreeQueue<LogEntry, 1000000> console_queue_;
  LockFreeQueue<LogEntry, 1000000> csv_queue_;

  void console_loop();
  void csv_loop();

  uint64_t format_timestamp(const std::string &timestamp);
  void write_to_buffer(const std::string &log_line);
  void flush_buffer();
  static std::string format_log_entry(const LogEntry &entry);
  void format_and_send_to_db(const LogEntry &entry);

public:
  AsyncLogger(Connection *connection,
              const std::string &csv_file,
              const std::string &instrument_id,
              size_t buffer_size = DEFAULT_BUFFER_SIZE);
  ~AsyncLogger();

  AsyncLogger(const AsyncLogger &) = delete;
  AsyncLogger &operator=(const AsyncLogger &) = delete;
  AsyncLogger(AsyncLogger &&) = delete;
  AsyncLogger &operator=(AsyncLogger &&) = delete;

  void log(const std::string &timestamp,
           int32_t bid,
           int32_t ask,
           int position,
           int trade_count,
           float pnl);
};
```

## High-Level Overview

This is a C++ header file that declares interfaces, classes, and data structures.

**Includes:**
- `string`
- `thread`
- `atomic`
- `sys/mman.h`
- `fcntl.h`
- `db_connection.h`
- `lock_free_queue.h`

**Classes Declared:**
- `AsyncLogger`

**Structs Declared:**
- `LogEntry`



## Detailed Code Walkthrough

### Include Directives

The file includes the following headers:

- `string`: Standard library header
- `thread`: Project-specific header
- `atomic`: Project-specific header
- `sys/mman.h`: Project-specific header
- `fcntl.h`: Project-specific header
- `db_connection.h`: Project-specific header
- `lock_free_queue.h`: Project-specific header

### Class: `AsyncLogger`

This class provides functionality related to asynclogger.

**Member Functions:**
- `format_and_send_to_db()`
- `write_to_buffer()`
- `flush_buffer()`
- `console_loop()`
- `AsyncLogger()`
- `format_timestamp()`
- `log()`
- `format_log_entry()`
- `csv_loop()`

### Standalone Functions

#### `console_loop()`

Function that performs operations related to console_loop.

#### `csv_loop()`

Function that performs operations related to csv_loop.

#### `format_timestamp(const std::string &timestamp)`

Function that performs operations related to format_timestamp.

#### `write_to_buffer(const std::string &log_line)`

Function that performs operations related to write_to_buffer.

#### `flush_buffer()`

Function that performs operations related to flush_buffer.

#### `format_log_entry(const LogEntry &entry)`

Function that performs operations related to format_log_entry.

#### `format_and_send_to_db(const LogEntry &entry)`

Function that performs operations related to format_and_send_to_db.

#### `AsyncLogger(Connection *connection,
              const std::string &csv_file,
              const std::string &instrument_id,
              size_t buffer_size = DEFAULT_BUFFER_SIZE)`

Function that performs operations related to AsyncLogger.

#### `log(const std::string &timestamp,
           int32_t bid,
           int32_t ask,
           int position,
           int trade_count,
           float pnl)`

Function that performs operations related to log.



## Usage Examples

To use this header file, include it in your source code:

```cpp
#include "async_logger.h"
```

Example usage of `AsyncLogger`:

```cpp
AsyncLogger obj;
// Use obj methods here
```


## Performance & Security Notes

**Performance**: Uses constexpr for compile-time evaluation.



## Related Files

**Included Headers:**
- [`string`](../string)
- [`thread`](../thread)
- [`atomic`](../atomic)
- `sys/mman.h`
- [`fcntl.h`](../fcntl.h)
- [`db_connection.h`](../db_connection.h)
- [`lock_free_queue.h`](../lock_free_queue.h)

**Implementation File**: [`async_logger.cpp`](../async_logger.cpp)



## Testing

To test this component:

1. Build the project using CMake
2. Run the executable
3. Verify expected behavior

Consider adding unit tests for critical functions.
