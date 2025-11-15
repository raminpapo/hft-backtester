# File Documentation: concurrent_backtest.h

## Metadata

- **File Path**: `include/concurrent_backtest.h`
- **File Name**: `concurrent_backtest.h`
- **Language**: C++ Header
- **Lines of Code**: 46
- **Characters**: 1399
- **Words**: 100
- **Last Modified**: 2025-11-15T20:07:20.475877

## Original Source Code

```h
#pragma once
#include "backtester.h"
#include "connection_pool.h"
#include <atomic>
#include <condition_variable>
#include <map>
#include <memory>
#include <mutex>
#include <thread>

class ConcurrentBacktester {
private:
  struct InstrumentConfig {
    std::string instrument_id;
    std::unique_ptr<Backtester> backtester;
    std::vector<book_message> messages;
    std::vector<book_message> train_messages;
    int32_t pnl{0};
    std::string backtest_file;
    std::string train_file;
    std::thread thread;
  };

  static std::shared_ptr<ConnectionPool> connection_pool_;
  static std::once_flag pool_init_flag_;
  std::map<std::string, InstrumentConfig> instruments_;
  std::atomic<bool> running_{false};
  std::atomic<int> completed_count_{0};
  std::mutex completion_mutex_;
  std::mutex cout_mutex_;

public:
  explicit ConcurrentBacktester();
  ~ConcurrentBacktester();

  ConcurrentBacktester(const ConcurrentBacktester &) = delete;
  ConcurrentBacktester &operator=(const ConcurrentBacktester &) = delete;

  void add_instrument(const std::string &instrument_id,
                      std::vector<book_message> &&messages,
                      std::vector<book_message> &&train_messages = {},
                      const std::string &backtest_file = "",
                      const std::string &train_file = "");
  void start_backtest(size_t strategy_index);
  void stop_backtest();
};
```

## High-Level Overview

This is a C++ header file that declares interfaces, classes, and data structures.

**Includes:**
- `backtester.h`
- `connection_pool.h`
- `atomic`
- `condition_variable`
- `map`
- `memory`
- `mutex`
- `thread`

**Classes Declared:**
- `ConcurrentBacktester`

**Structs Declared:**
- `InstrumentConfig`



## Detailed Code Walkthrough

### Include Directives

The file includes the following headers:

- `backtester.h`: Project-specific header
- `connection_pool.h`: Project-specific header
- `atomic`: Project-specific header
- `condition_variable`: Project-specific header
- `map`: Standard library header
- `memory`: Project-specific header
- `mutex`: Project-specific header
- `thread`: Project-specific header

### Class: `ConcurrentBacktester`

This class provides functionality related to concurrentbacktester.

**Member Functions:**
- `stop_backtest()`
- `ConcurrentBacktester()`
- `start_backtest()`
- `add_instrument()`

### Standalone Functions

#### `ConcurrentBacktester()`

Function that performs operations related to ConcurrentBacktester.

#### `add_instrument(const std::string &instrument_id,
                      std::vector<book_message> &&messages,
                      std::vector<book_message> &&train_messages = {},
                      const std::string &backtest_file = "",
                      const std::string &train_file = "")`

Function that performs operations related to add_instrument.

#### `start_backtest(size_t strategy_index)`

Function that performs operations related to start_backtest.

#### `stop_backtest()`

Function that performs operations related to stop_backtest.



## Usage Examples

To use this header file, include it in your source code:

```cpp
#include "concurrent_backtest.h"
```

Example usage of `ConcurrentBacktester`:

```cpp
ConcurrentBacktester obj;
// Use obj methods here
```


## Performance & Security Notes

No specific performance or security concerns identified. Follow standard C++ best practices.


## Related Files

**Included Headers:**
- [`backtester.h`](../backtester.h)
- [`connection_pool.h`](../connection_pool.h)
- [`atomic`](../atomic)
- [`condition_variable`](../condition_variable)
- [`map`](../map)
- [`memory`](../memory)
- [`mutex`](../mutex)
- [`thread`](../thread)

**Implementation File**: [`concurrent_backtest.cpp`](../concurrent_backtest.cpp)



## Testing

This is a test file.

