# File Documentation: backtester.h

## Metadata

- **File Path**: `include/backtester.h`
- **File Name**: `backtester.h`
- **Language**: C++ Header
- **Lines of Code**: 56
- **Characters**: 1547
- **Words**: 112
- **Last Modified**: 2025-11-15T20:07:20.474877

## Original Source Code

```h
#pragma once
#include "../include/book/orderbook.h"
#include "message.h"
#include "strategy.h"
#include <memory>
#include <vector>

struct TradingDay {
  std::vector<book_message> messages_;
  std::string date_;
  std::string start_time_;
  std::string end_time_;
  std::string file_;
};

class Backtester {
public:
  Backtester(std::shared_ptr<ConnectionPool> pool,
             const std::string &instrument_id,
             const std::vector<book_message> &&messages,
             const std::vector<book_message> &&train_messages = {});
  ~Backtester();

  void create_strategy(size_t strategy_index);
  void set_trading_times(const std::string &backtest_file,
                         const std::string &train_file = "");
  void train_model();
  void start_backtest();
  void stop_backtest();
  void reset_state();

private:
  void run_backtest();
  void run_multiday_backtest();

  std::queue<TradingDay> trading_days_;
  TradingDay current_day_;
  std::shared_ptr<Orderbook> book_;
  std::unique_ptr<Orderbook> train_book_;
  size_t train_message_index_;
  std::unique_ptr<Strategy> strategy_;
  std::shared_ptr<ConnectionPool> connection_pool_;
  std::string instrument_id_;
  Connection *db_connection_;
  bool first_update_;
  size_t current_message_index_;
  std::atomic<bool> running_;
  std::vector<book_message> messages_;
  std::vector<book_message> train_messages_;
  std::string start_time_;
  std::string end_time_;
  std::string train_start_time_;
  std::string train_end_time_;

  static constexpr int UPDATE_INTERVAL = 1000;
};
```

## High-Level Overview

This is a C++ header file that declares interfaces, classes, and data structures.

**Includes:**
- `../include/book/orderbook.h`
- `message.h`
- `strategy.h`
- `memory`
- `vector`

**Classes Declared:**
- `Backtester`

**Structs Declared:**
- `TradingDay`



## Detailed Code Walkthrough

### Include Directives

The file includes the following headers:

- `../include/book/orderbook.h`: Project-specific header
- `message.h`: Project-specific header
- `strategy.h`: Project-specific header
- `memory`: Project-specific header
- `vector`: Standard library header

### Class: `Backtester`

This class provides functionality related to backtester.

**Member Functions:**
- `set_trading_times()`
- `create_strategy()`
- `Backtester()`
- `start_backtest()`
- `run_multiday_backtest()`
- `stop_backtest()`
- `train_model()`
- `reset_state()`
- `run_backtest()`

### Standalone Functions

#### `Backtester(std::shared_ptr<ConnectionPool> pool,
             const std::string &instrument_id,
             const std::vector<book_message> &&messages,
             const std::vector<book_message> &&train_messages = {})`

Function that performs operations related to Backtester.

#### `create_strategy(size_t strategy_index)`

Function that performs operations related to create_strategy.

#### `set_trading_times(const std::string &backtest_file,
                         const std::string &train_file = "")`

Function that performs operations related to set_trading_times.

#### `train_model()`

Function that performs operations related to train_model.

#### `start_backtest()`

Function that performs operations related to start_backtest.

#### `stop_backtest()`

Function that performs operations related to stop_backtest.

#### `reset_state()`

Function that performs operations related to reset_state.

#### `run_backtest()`

Function that performs operations related to run_backtest.

#### `run_multiday_backtest()`

Function that performs operations related to run_multiday_backtest.



## Usage Examples

To use this header file, include it in your source code:

```cpp
#include "backtester.h"
```

Example usage of `Backtester`:

```cpp
Backtester obj;
// Use obj methods here
```


## Performance & Security Notes

**Performance**: Uses constexpr for compile-time evaluation.



## Related Files

**Included Headers:**
- `../include/book/orderbook.h`
- [`message.h`](../message.h)
- [`strategy.h`](../strategy.h)
- [`memory`](../memory)
- [`vector`](../vector)

**Implementation File**: [`backtester.cpp`](../backtester.cpp)



## Testing

This is a test file.

