# File Documentation: strategy.h

## Metadata

- **File Path**: `include/strategy.h`
- **File Name**: `strategy.h`
- **Language**: C++ Header
- **Lines of Code**: 92
- **Characters**: 2311
- **Words**: 233
- **Last Modified**: 2025-11-15T20:07:20.477877

## Original Source Code

```h
#pragma once
#include "../include/book/orderbook.h"
#include "async_logger.h"
#include "connection_pool.h"
#include <cstring>
#include <iostream>
#include <memory>

class Strategy {
protected:
  int position_ = 0;
  int buy_qty_;
  int sell_qty_;
  int32_t real_total_buy_px_;
  int32_t real_total_sell_px_;
  int32_t theo_total_buy_px_;
  int32_t theo_total_sell_px_;
  float fees_;
  int32_t pnl_;
  static constexpr int max_pos_ = 1;
  int32_t POINT_VALUE_;
  static constexpr int32_t FEES_PER_SIDE_ = 1;
  int32_t prev_pnl_;
  std::unique_ptr<AsyncLogger> logger_;
  std::shared_ptr<ConnectionPool> connection_pool_;
  Orderbook *book_;
  std::string id_;

  virtual void update_theo_values() = 0;
  virtual void calculate_pnl() = 0;

public:
  Strategy(std::shared_ptr<ConnectionPool> pool,
           const std::string &log_file_name,
           const std::string &instrument_id,
           Orderbook *book) :
    position_(0)
    , buy_qty_(0)
    , sell_qty_(0)
    , real_total_buy_px_(0)
    , real_total_sell_px_(0)
    , theo_total_buy_px_(0)
    , theo_total_sell_px_(0)
    , fees_(0)
    , pnl_(0)
    , prev_pnl_(0)
    , connection_pool_(pool)
    , book_(book)
    , req_fitting_(false) {

    Connection *db_connection = connection_pool_->acquire_connection();
    if (!db_connection) {
      throw std::runtime_error(
          "failed to acquire database connection for logger");
    }

    logger_ = std::make_unique<AsyncLogger>(db_connection, log_file_name,
                                            instrument_id);

  }

  std::queue<std::tuple<bool, int32_t>> trade_queue_;
  virtual void log_stats(const Orderbook &book) = 0;
  virtual void close_positions() = 0;

  std::string name_;
  bool req_fitting_;

  virtual void fit_model() = 0;

  virtual void reset() {
    position_ = 0;
    buy_qty_ = 0;
    sell_qty_ = 0;
    real_total_sell_px_ = 0;
    real_total_buy_px_ = 0;
    theo_total_buy_px_ = 0;
    theo_total_sell_px_ = 0;
    fees_ = 0;
    pnl_ = 0;
    prev_pnl_ = 0;
  }

  virtual ~Strategy() = default;

  virtual void on_book_update() = 0;
  virtual void execute_trade(bool side, int32_t price, int size) = 0;

  int32_t get_pnl() const { return pnl_; }
  int get_position() const { return position_; }
  bool requires_fitting() const { return req_fitting_; }
};
```

## High-Level Overview

This is a C++ header file that declares interfaces, classes, and data structures.

**Includes:**
- `../include/book/orderbook.h`
- `async_logger.h`
- `connection_pool.h`
- `cstring`
- `iostream`
- `memory`

**Classes Declared:**
- `Strategy`



## Detailed Code Walkthrough

### Include Directives

The file includes the following headers:

- `../include/book/orderbook.h`: Project-specific header
- `async_logger.h`: Project-specific header
- `connection_pool.h`: Project-specific header
- `cstring`: Project-specific header
- `iostream`: Standard library header
- `memory`: Project-specific header

### Class: `Strategy`

This class provides functionality related to strategy.

**Member Functions:**
- `log_stats()`
- `update_theo_values()`
- `pnl_()`
- `calculate_pnl()`
- `book_()`
- `prev_pnl_()`
- `theo_total_buy_px_()`
- `acquire_connection()`
- `if()`
- `real_total_buy_px_()`
- `theo_total_sell_px_()`
- `Strategy()`
- `buy_qty_()`
- `runtime_error()`
- `connection_pool_()`
- `sell_qty_()`
- `fees_()`
- `position_()`
- `req_fitting_()`
- `real_total_sell_px_()`

### Standalone Functions

#### `reset()`

Function that performs operations related to reset.

#### `get_pnl()`

Function that performs operations related to get_pnl.

#### `get_position()`

Function that performs operations related to get_position.

#### `requires_fitting()`

Function that performs operations related to requires_fitting.



## Usage Examples

To use this header file, include it in your source code:

```cpp
#include "strategy.h"
```

Example usage of `Strategy`:

```cpp
Strategy obj;
// Use obj methods here
```


## Performance & Security Notes

**Performance**: Uses constexpr for compile-time evaluation.



## Related Files

**Included Headers:**
- `../include/book/orderbook.h`
- [`async_logger.h`](../async_logger.h)
- [`connection_pool.h`](../connection_pool.h)
- [`cstring`](../cstring)
- [`iostream`](../iostream)
- [`memory`](../memory)

**Implementation File**: [`strategy.cpp`](../strategy.cpp)



## Testing

To test this component:

1. Build the project using CMake
2. Run the executable
3. Verify expected behavior

Consider adding unit tests for critical functions.
