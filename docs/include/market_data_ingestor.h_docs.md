# File Documentation: market_data_ingestor.h

## Metadata

- **File Path**: `include/market_data_ingestor.h`
- **File Name**: `market_data_ingestor.h`
- **Language**: C++ Header
- **Lines of Code**: 48
- **Characters**: 1342
- **Words**: 103
- **Last Modified**: 2025-11-15T20:07:20.476876

## Original Source Code

```h
#pragma once
#include <vector>
#include <memory>
#include <atomic>
#include <thread>
#include <cstdint>
#include <cstddef>
#include "message.h"

class Orderbook;

class MarketDataIngestor {
public:
  explicit MarketDataIngestor(std::vector<book_message> messages);
  ~MarketDataIngestor();

  MarketDataIngestor(const MarketDataIngestor &) = delete;
  MarketDataIngestor &operator=(const MarketDataIngestor &) = delete;
  MarketDataIngestor(MarketDataIngestor &&) = delete;
  MarketDataIngestor &operator=(MarketDataIngestor &&) = delete;

  void start();
  void stop();
  bool is_completed() const;
  const Orderbook &get_orderbook() const;

  void print_performance_stats() const;
  uint64_t get_messages_processsed() const;
  size_t get_total_messages() const;
  double get_curr_rate() const;

private:
  std::unique_ptr<Orderbook> orderbook_;
  std::vector<book_message> messages_;

  alignas(64) std::atomic<bool> running_{false};
  alignas(64) std::atomic<bool> completed_{false};
  std::thread injestor_thread_;

  alignas(64) std::atomic<uint64_t> messages_processed_{0};
  alignas(64) std::atomic<uint64_t> start_time_ns_{0};
  alignas(64) std::atomic<uint64_t> end_time_ns_{0};

  void ingest_market_data();
  static void pin_cpu(unsigned cpu_id);
  void start_perf_tracking();
  void end_perf_tracking(uint64_t processed_count);
};
```

## High-Level Overview

This is a C++ header file that declares interfaces, classes, and data structures.

**Includes:**
- `vector`
- `memory`
- `atomic`
- `thread`
- `cstdint`
- `cstddef`
- `message.h`

**Classes Declared:**
- `Orderbook`
- `MarketDataIngestor`



## Detailed Code Walkthrough

### Include Directives

The file includes the following headers:

- `vector`: Standard library header
- `memory`: Project-specific header
- `atomic`: Project-specific header
- `thread`: Project-specific header
- `cstdint`: Project-specific header
- `cstddef`: Project-specific header
- `message.h`: Project-specific header

### Class: `Orderbook`

This class provides functionality related to orderbook.

**Member Functions:**
- `print_performance_stats()`
- `pin_cpu()`
- `ingest_market_data()`
- `get_curr_rate()`
- `get_total_messages()`
- `stop()`
- `start()`
- `get_messages_processsed()`
- `start_perf_tracking()`
- `get_orderbook()`
- `MarketDataIngestor()`
- `alignas()`
- `is_completed()`

### Standalone Functions

#### `MarketDataIngestor(std::vector<book_message> messages)`

Function that performs operations related to MarketDataIngestor.

#### `start()`

Function that performs operations related to start.

#### `stop()`

Function that performs operations related to stop.

#### `is_completed()`

Function that performs operations related to is_completed.

#### `print_performance_stats()`

Function that performs operations related to print_performance_stats.

#### `get_messages_processsed()`

Function that performs operations related to get_messages_processsed.

#### `get_total_messages()`

Function that performs operations related to get_total_messages.

#### `get_curr_rate()`

Function that performs operations related to get_curr_rate.

#### `ingest_market_data()`

Function that performs operations related to ingest_market_data.

#### `pin_cpu(unsigned cpu_id)`

Function that performs operations related to pin_cpu.



## Usage Examples

To use this header file, include it in your source code:

```cpp
#include "market_data_ingestor.h"
```

Example usage of `Orderbook`:

```cpp
Orderbook obj;
// Use obj methods here
```


## Performance & Security Notes

No specific performance or security concerns identified. Follow standard C++ best practices.


## Related Files

**Included Headers:**
- [`vector`](../vector)
- [`memory`](../memory)
- [`atomic`](../atomic)
- [`thread`](../thread)
- [`cstdint`](../cstdint)
- [`cstddef`](../cstddef)
- [`message.h`](../message.h)

**Implementation File**: [`market_data_ingestor.cpp`](../market_data_ingestor.cpp)



## Testing

To test this component:

1. Build the project using CMake
2. Run the executable
3. Verify expected behavior

Consider adding unit tests for critical functions.
