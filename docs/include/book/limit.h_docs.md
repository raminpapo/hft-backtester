# File Documentation: limit.h

## Metadata

- **File Path**: `include/book/limit.h`
- **File Name**: `limit.h`
- **Language**: C++ Header
- **Lines of Code**: 58
- **Characters**: 1249
- **Words**: 162
- **Last Modified**: 2025-11-15T20:07:20.474877

## Original Source Code

```h
#pragma once
#include <cstdint>
#include <cstddef>
#include <cstdlib>
#include <new>
#include <atomic>
#include <cassert>


struct alignas(64) Limit {
  int32_t price_ = 0;
  int32_t volume_ = 0;

  Order *head_ = nullptr;
  Order *tail_ = nullptr;

  bool side_ = false;

  [[nodiscard]] inline bool is_empty() const noexcept {
    return head_ == nullptr;
  }

  inline void add_order(Order *new_order) noexcept {
    new_order->prev_ = tail_;
    new_order->next_ = nullptr;
    if (tail_) {
      tail_->next_ = new_order;
    }
    else {
      head_ = new_order;
    }

    tail_ = new_order;
    volume_ += new_order->size;
  }

  inline void remove_order(Order *target) noexcept {
    if (target->prev_) {
      target->prev_->next_ = target->next_;
    } else {
      head_ = target->next_;
    }
    if (target->next_) {
      target->next_->prev_ = target->prev_;
    } else {
      tail_ = target->prev_;
    }
    volume_ -= target->size;
  }

  private:
  static constexpr std::size_t used = 4  + 4 + 2 * sizeof(Order *) + 1 ;
  static constexpr std::size_t PAD = 64 - used;
  std::byte _pad[PAD]{};
};

static_assert(sizeof(Limit) == 64, "Limit must be 64 bytes");
static_assert(alignof(Limit) == 64, "Limit must be 64-byte aligned");
```

## High-Level Overview

This is a C++ header file that declares interfaces, classes, and data structures.

**Includes:**
- `cstdint`
- `cstddef`
- `cstdlib`
- `new`
- `atomic`
- `cassert`

**Structs Declared:**
- `alignas`



## Detailed Code Walkthrough

### Include Directives

The file includes the following headers:

- `cstdint`: Project-specific header
- `cstddef`: Project-specific header
- `cstdlib`: Project-specific header
- `new`: Project-specific header
- `atomic`: Project-specific header
- `cassert`: Project-specific header



## Usage Examples

To use this header file, include it in your source code:

```cpp
#include "limit.h"
```



## Performance & Security Notes

**Performance**: Uses inline functions for performance optimization.

**Performance**: Uses constexpr for compile-time evaluation.



## Related Files

**Included Headers:**
- [`cstdint`](../cstdint)
- [`cstddef`](../cstddef)
- [`cstdlib`](../cstdlib)
- [`new`](../new)
- [`atomic`](../atomic)
- [`cassert`](../cassert)

**Implementation File**: [`limit.cpp`](../limit.cpp)



## Testing

To test this component:

1. Build the project using CMake
2. Run the executable
3. Verify expected behavior

Consider adding unit tests for critical functions.
