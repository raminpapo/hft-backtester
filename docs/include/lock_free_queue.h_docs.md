# File Documentation: lock_free_queue.h

## Metadata

- **File Path**: `include/lock_free_queue.h`
- **File Name**: `lock_free_queue.h`
- **Language**: C++ Header
- **Lines of Code**: 131
- **Characters**: 3587
- **Words**: 294
- **Last Modified**: 2025-11-15T20:07:20.476876

## Original Source Code

```h
#pragma once

#include <atomic>
#include <array>
#include <optional>

template <typename T, size_t SIZE>
class LockFreeQueue {
private:
  struct alignas(64) Node {
    std::atomic<bool> written{false};
    alignas(T) unsigned char storage[sizeof(T)];

    Node() noexcept :
      written(false) {
    }

    ~Node() {
      if (written.load(std::memory_order_relaxed)) {
        reinterpret_cast<T *>(storage)->~T();
      }
    }

    Node(const Node &) = delete;
    Node &operator=(const Node &) = delete;
  };

  // assign head and tail to different cache lines to avoid false sharing
  alignas(64) std::atomic<size_t> head_{0};
  alignas(64) std::atomic<size_t> tail_{0};
  alignas(64) std::array<Node, SIZE> buffer_;

public:
  LockFreeQueue() = default;

  ~LockFreeQueue() {
    size_t curr_head = head_.load(std::memory_order_relaxed);
    size_t curr_tail = tail_.load(std::memory_order_relaxed);

    while (curr_head != curr_tail) {
      Node &node = buffer_[curr_head];
      if (node.written.load(std::memory_order_relaxed)) {
        reinterpret_cast<T *>(node.storage)->~T();
        node.written.store(false, std::memory_order_relaxed);
      }
      curr_head = (curr_head + 1) % SIZE;

    }
  }

  LockFreeQueue(const LockFreeQueue &) = delete;
  LockFreeQueue &operator=(const LockFreeQueue &) = delete;

  template <typename U>
  __attribute__((always_inline))
  bool enqueue(U &&item) {
    while (true) {
      size_t curr_tail = tail_.load(std::memory_order_relaxed);
      size_t next_tail = (curr_tail + 1) % SIZE;

      if (next_tail == head_.load(std::memory_order_acquire)) {
        return false;
      }

      if (buffer_[curr_tail].written.load(std::memory_order_relaxed)) {
        continue;
      }

      // first check to see if another thread already modified the tail
      if (!tail_.compare_exchange_weak(curr_tail, next_tail,
                                       std::memory_order_acq_rel,
                                       std::memory_order_relaxed)) {
        continue;
      }

      new(buffer_[curr_tail].storage) T(std::forward<U>(item));
      buffer_[curr_tail].written.store(true, std::memory_order_release);
      return true;

    }
  }


  __attribute__((always_inline))
  std::optional<T> dequeue() {
    while (true) {
      size_t curr_head = head_.load(std::memory_order_relaxed);

      if (curr_head == tail_.load(std::memory_order_acquire)) {
        return std::nullopt;
      }

      if (!buffer_[curr_head].written.load(std::memory_order_acquire)) {
        continue;
      }

      if (!head_.compare_exchange_weak(curr_head, (curr_head + 1) % SIZE,
                                       std::memory_order_release,
                                       std::memory_order_relaxed)) {
        continue;
      }

      std::optional<T> result(
          std::move(*reinterpret_cast<T *>(buffer_[curr_head].storage)));
      reinterpret_cast<T *>(buffer_[curr_head].storage)->~T();
      buffer_[curr_head].written.store(false, std::memory_order_release);
      return result;
    }
  }

  __attribute__((always_inline))
  bool empty() {
    return head_.load(std::memory_order_acquire) == tail_.load(
               std::memory_order_acquire);
  }

  __attribute__((always_inline))
  size_t size() const {
    size_t head = head_.load(std::memory_order_acquire);
    size_t tail = tail_.load(std::memory_order_acquire);

    if (tail >= head) {
      return tail - head;
    } else {
      return SIZE - (head - tail);
    }
  }

  __attribute__((always_inline))
  size_t capacity() const { return SIZE - 1; }
};
```

## High-Level Overview

This is a C++ header file that declares interfaces, classes, and data structures.

**Includes:**
- `atomic`
- `array`
- `optional`

**Classes Declared:**
- `LockFreeQueue`

**Structs Declared:**
- `alignas`

**Template Declarations:** Yes (generic programming)



## Detailed Code Walkthrough

### Include Directives

The file includes the following headers:

- `atomic`: Project-specific header
- `array`: Project-specific header
- `optional`: Project-specific header

### Class: `LockFreeQueue`

This class provides functionality related to lockfreequeue.

**Member Functions:**
- `LockFreeQueue()`
- `store()`
- `load()`
- `written()`
- `while()`
- `Node()`
- `if()`
- `T()`
- `alignas()`
- `sizeof()`

### Standalone Functions

#### `enqueue(U &&item)`

Function that performs operations related to enqueue.

#### `empty()`

Function that performs operations related to empty.

#### `size()`

Function that performs operations related to size.

#### `capacity()`

Function that performs operations related to capacity.



## Usage Examples

To use this header file, include it in your source code:

```cpp
#include "lock_free_queue.h"
```

Example usage of `LockFreeQueue`:

```cpp
LockFreeQueue obj;
// Use obj methods here
```


## Performance & Security Notes

**Performance**: Uses inline functions for performance optimization.

**Performance**: Uses templates for compile-time polymorphism and type safety.

**Performance**: Uses move semantics for efficient resource management.



## Related Files

**Included Headers:**
- [`atomic`](../atomic)
- [`array`](../array)
- [`optional`](../optional)

**Implementation File**: [`lock_free_queue.cpp`](../lock_free_queue.cpp)



## Testing

To test this component:

1. Build the project using CMake
2. Run the executable
3. Verify expected behavior

Consider adding unit tests for critical functions.
