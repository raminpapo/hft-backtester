# File Documentation: slab_map.h

## Metadata

- **File Path**: `include/slab_map.h`
- **File Name**: `slab_map.h`
- **Language**: C++ Header
- **Lines of Code**: 113
- **Characters**: 2718
- **Words**: 328
- **Last Modified**: 2025-11-15T20:07:20.476876

## Original Source Code

```h
#pragma once
#include "../include/book/order.h"
#include <cassert>
#include <cstdint>
#include <cstdlib>
#include <cstring>
#include <sys/mman.h>
#include <vector>

constexpr uint64_t PAGE_SHIFT = 18;
constexpr uint64_t SLOTS_PER_PAGE = 1ull << PAGE_SHIFT;
constexpr uint64_t PAGE_MASK = SLOTS_PER_PAGE - 1;
constexpr std::size_t PAGE_BYTES = SLOTS_PER_PAGE * sizeof(Order *);

using Page = Order *[SLOTS_PER_PAGE];

inline Page *alloc_page() {
  void *mem =
      mmap(nullptr, PAGE_BYTES, PROT_READ | PROT_WRITE,
           MAP_PRIVATE | MAP_ANONYMOUS | MAP_HUGETLB | MAP_POPULATE, -1, 0);
  if (mem == MAP_FAILED) {
    mem = std::aligned_alloc(64, PAGE_BYTES);
    if (!mem) {
      throw std::bad_alloc();
    }
    //std::memset(mem, 0, PAGE_BYTES);
  }
  return static_cast<Page *>(mem);
}

class PageMap {
private:
  std::vector<Page *> pages_;
  std::vector<Page *> free_pages_;
  uint64_t base_id_ = 0;
  size_t base_page_ = 0;

  Page *acquire_page() {
    if (!free_pages_.empty()) {
      Page *p = free_pages_.back();
      free_pages_.pop_back();
      return p;
    }
    return alloc_page();
  }

  Order **slot_ptr(uint64_t id) {
    if (pages_.empty()) {
      base_id_ = id & ~PAGE_MASK;
      base_page_ = (base_id_ >> PAGE_SHIFT);
      pages_.resize(1, nullptr);
    }

    if (id < base_id_) {
      uint64_t new_base = id & ~PAGE_MASK;
      size_t new_page = new_base >> PAGE_SHIFT;
      size_t shift = base_page_ - new_page;
      pages_.insert(pages_.begin(), shift, nullptr);
      base_id_ = new_base;
      base_page_ = new_page;
    }

    size_t page_idx = (id >> PAGE_SHIFT) - base_page_;
    size_t slot = id & PAGE_MASK;

    if (page_idx >= pages_.size()) {
      pages_.resize(page_idx + 1, nullptr);
    }


    Page *p = pages_[page_idx];
    if (!p) {
      p = acquire_page();
      pages_[page_idx] = p;
    }
    return &(*p)[slot];
  }

public:
  explicit PageMap(std::size_t preallocate_pages = 0) {
    for (std::size_t i = 0; i < preallocate_pages; ++i) {
      Page *p = alloc_page();
      static_cast<volatile char *>(reinterpret_cast<void *>(p))[0] = 0;
      free_pages_.push_back(p);
    }
  }

  ~PageMap() { clear(); }
  PageMap(const PageMap &) = delete;
  PageMap &operator=(const PageMap &) = delete;

  void insert(uint64_t id, Order *ptr) { *slot_ptr(id) = ptr; }
  void erase(uint64_t id) { *slot_ptr(id) = nullptr; }
  Order *find(uint64_t id) { return *slot_ptr(id); }

  void clear() {
    for (Page *p : pages_)
      if (p) {
        munmap(p, PAGE_BYTES);
      }

    for (Page *p : free_pages_) {
      if (p) {
        munmap(p, PAGE_BYTES);
      }
    }
    pages_.clear();
    free_pages_.clear();
    base_id_ = 0;
    base_page_ = 0;
  }
};

```

## High-Level Overview

This is a C++ header file that declares interfaces, classes, and data structures.

**Includes:**
- `../include/book/order.h`
- `cassert`
- `cstdint`
- `cstdlib`
- `cstring`
- `sys/mman.h`
- `vector`

**Classes Declared:**
- `PageMap`



## Detailed Code Walkthrough

### Include Directives

The file includes the following headers:

- `../include/book/order.h`: Project-specific header
- `cassert`: Project-specific header
- `cstdint`: Project-specific header
- `cstdlib`: Project-specific header
- `cstring`: Project-specific header
- `sys/mman.h`: Project-specific header
- `vector`: Standard library header

### Class: `PageMap`

This class provides functionality related to pagemap.

**Member Functions:**
- `resize()`
- `alloc_page()`
- `back()`
- `pop_back()`
- `PageMap()`
- `clear()`
- `if()`
- `for()`
- `insert()`
- `slot_ptr()`
- `push_back()`
- `acquire_page()`

### Standalone Functions

#### `alloc_page()`

Function that performs operations related to alloc_page.

#### `PageMap(std::size_t preallocate_pages = 0)`

Function that performs operations related to PageMap.

#### `insert(uint64_t id, Order *ptr)`

Function that performs operations related to insert.

#### `erase(uint64_t id)`

Function that performs operations related to erase.

#### `clear()`

Function that performs operations related to clear.



## Usage Examples

To use this header file, include it in your source code:

```cpp
#include "slab_map.h"
```

Example usage of `PageMap`:

```cpp
PageMap obj;
// Use obj methods here
```


## Performance & Security Notes

**Performance**: Uses inline functions for performance optimization.

**Performance**: Uses constexpr for compile-time evaluation.



## Related Files

**Included Headers:**
- `../include/book/order.h`
- [`cassert`](../cassert)
- [`cstdint`](../cstdint)
- [`cstdlib`](../cstdlib)
- [`cstring`](../cstring)
- `sys/mman.h`
- [`vector`](../vector)

**Implementation File**: [`slab_map.cpp`](../slab_map.cpp)



## Testing

To test this component:

1. Build the project using CMake
2. Run the executable
3. Verify expected behavior

Consider adding unit tests for critical functions.
