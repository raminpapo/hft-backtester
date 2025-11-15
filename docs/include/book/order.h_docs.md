# File Documentation: order.h

## Metadata

- **File Path**: `include/book/order.h`
- **File Name**: `order.h`
- **Language**: C++ Header
- **Lines of Code**: 30
- **Characters**: 681
- **Words**: 70
- **Last Modified**: 2025-11-15T20:07:20.474877

## Original Source Code

```h
#pragma once
#include <cstdint>
#include <iomanip>
#include <sstream>

class Limit;

class Order {
public:
  uint64_t id_;
  int32_t price_;
  uint32_t size;
  bool side_;
  uint64_t unix_time_;
  Order* next_;
  Order* prev_;
  Limit* parent_;
  bool filled_;

  inline Order(uint64_t id, int32_t price, uint32_t size, bool side,
        uint64_t unix_time)
      : id_(id), price_(price), size(size), side_(side), unix_time_(unix_time),
        next_(nullptr), prev_(nullptr), parent_(nullptr), filled_(false) {
  }

  inline Order()
      : id_(0), price_(0), size(0), side_(true), unix_time_(0), next_(nullptr),
        prev_(nullptr), parent_(nullptr), filled_(false) {
  }
};
```

## High-Level Overview

This is a C++ header file that declares interfaces, classes, and data structures.

**Includes:**
- `cstdint`
- `iomanip`
- `sstream`

**Classes Declared:**
- `Limit`
- `Order`



## Detailed Code Walkthrough

### Include Directives

The file includes the following headers:

- `cstdint`: Project-specific header
- `iomanip`: Project-specific header
- `sstream`: Project-specific header

### Class: `Limit`

This class provides functionality related to limit.

**Member Functions:**
- `size()`
- `side_()`
- `Order()`
- `unix_time_()`
- `id_()`
- `parent_()`
- `filled_()`
- `price_()`
- `prev_()`
- `next_()`



## Usage Examples

To use this header file, include it in your source code:

```cpp
#include "order.h"
```

Example usage of `Limit`:

```cpp
Limit obj;
// Use obj methods here
```


## Performance & Security Notes

**Performance**: Uses inline functions for performance optimization.



## Related Files

**Included Headers:**
- [`cstdint`](../cstdint)
- [`iomanip`](../iomanip)
- [`sstream`](../sstream)

**Implementation File**: [`order.cpp`](../order.cpp)



## Testing

To test this component:

1. Build the project using CMake
2. Run the executable
3. Verify expected behavior

Consider adding unit tests for critical functions.
