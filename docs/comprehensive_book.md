# HFT Backtester - Comprehensive Documentation Book

## Table of Contents

1. [Introduction](#introduction)
2. [Project Overview](#project-overview)
3. [Architecture](#architecture)
4. [Components](#components)
5. [Building and Running](#building-and-running)

## Introduction

This is the comprehensive documentation book for the HFT (High-Frequency Trading) Backtester project. This document consolidates all documentation from across the repository into a single, navigable resource.

## Project Overview

The HFT Backtester is a high-performance C++ application designed for backtesting high-frequency trading strategies. It features:

- Custom order book implementation
- Lock-free data structures for performance
- Multiple trading strategies
- Concurrent backtesting capabilities
- Market data ingestion
- Database integration

## Architecture

### Core Components

The project is organized into several key components:


### Root Directory

# Root Directory - Documentation

## Purpose

This is the root directory of the HFT Backtester project. It contains:

- Build configuration files (CMakeLists.txt)
- Documentation (README.md)
- Source code directories
- Project configuration

## Architecture

The project is organized into:

- **include/**: Header files defining interfaces and data structures
- **src/**: Implementation files with business logic
- Configuration files for building and version control

## Files Overview

This folder contains 4 file(s) and 2 subdirectory(ies).




### src

# src - Documentation

## Purpose

This directory contains C++ source files with the implementation of the project's functionality.

## Organization

Source files implement the interfaces defined in the header files, containing the actual business logic and algorithms.

## Files Overview

This folder contains 9 file(s) and 2 subdirectory(ies).




### src/benchmark

# src/benchmark - Documentation

## Purpose

This directory contains benchmarking code to measure and test performance of the system.

## Files Overview

This folder contains 1 file(s) and 0 subdirectory(ies).




### src/strategies

# src/strategies - Documentation

## Purpose

This directory contains trading strategy implementations that use the backtesting framework.

## Files Overview

This folder contains 2 file(s) and 0 subdirectory(ies).




### include

# include - Documentation

## Purpose

This directory contains C++ header files that define the public interfaces, classes, and data structures used throughout the project.

## Organization

Headers are organized by component and functionality, providing clean separation of interface from implementation.

## Files Overview

This folder contains 10 file(s) and 1 subdirectory(ies).




### include/book

# include/book - Documentation

## Purpose

This directory contains components related to the order book implementation - the core data structure for managing orders in the HFT system.

## Files Overview

This folder contains 6 file(s) and 0 subdirectory(ies).





## Components

### Detailed Component Documentation

For detailed documentation of each component, see the individual file documentation in the respective directories.

## Building and Running

See [CMakeLists.txt documentation](./CMakeLists.txt_docs.md) for build instructions.

### Quick Start

```bash
mkdir build
cd build
cmake ..
make
./databento_orderbook
```

## Conclusion

This documentation provides a comprehensive overview of the HFT Backtester project. For more detailed information about specific components, refer to the individual file documentation.

---

Generated on: 2025-11-15T20:11:08.872979
