# File Documentation: CMakeLists.txt

## Metadata

- **File Path**: `CMakeLists.txt`
- **File Name**: `CMakeLists.txt`
- **Language**: Text
- **Lines of Code**: 150
- **Characters**: 4164
- **Words**: 195
- **Last Modified**: 2025-11-15T20:07:20.473876

## Original Source Code

```txt
cmake_minimum_required(VERSION 3.27)

project(databento_orderbook)

include(FetchContent)
FetchContent_Declare(
        databento
        GIT_REPOSITORY https://github.com/databento/databento-cpp
        GIT_TAG HEAD
)
FetchContent_MakeAvailable(databento)


set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)
set(CMAKE_BUILD_TYPE RelWithDebInfo)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O3")

set(AWS_SDK_PATH "/opt/homebrew/Cellar/aws-sdk-cpp/1.11.450")

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

set(QT_DIR "$ENV{HOME}/QT/6.7.3/macos")
set(CMAKE_PREFIX_PATH "${QT_DIR}" ${AWS_SDK_PATH})

set(BOOST_ROOT "/opt/homebrew/opt/boost")
set(Boost_INCLUDE_DIR "${BOOST_ROOT}/include")
set(Boost_LIBRARY_DIR "${BOOST_ROOT}/lib")

set(PQXX_ROOT "/opt/homebrew/Cellar/libpqxx/7.9.2")
set(PQXX_INCLUDE_DIR "${PQXX_ROOT}/include")
set(PQXX_LIBRARY_DIR "${PQXX_ROOT}/lib")

set(JSON_ROOT "/opt/homebrew/Cellar/nlohmann-json/3.11.3")
set(JSON_INCLUDE_DIR "${JSON_ROOT}/include")

set(CURL_ROOT "/opt/homebrew/Cellar/curl/8.9.1")
set(CURL_INCLUDE_DIR "${CURL_ROOT}/include")
set(CURL_LIBRARY_DIR "${CURL_ROOT}/lib")

set(WEBSOCKETPP_INCLUDE_DIR "/usr/local/include")

set(EIGEN_ROOT "/opt/homebrew/Cellar/eigen/3.4.0_1")
set(EIGEN_INCLUDE_DIR "${EIGEN_ROOT}/include/eigen3")

set(XXHASH_ROOT "/opt/homebrew/Cellar/xxhash")
set(XXHASH_INCLUDE_DIR "${XXHASH_ROOT}/include")
set(XXHASH_LIBRARY_DIR "${XXHASH_ROOT}/lib")

set(SOURCES
        ${SOURCES}
        src/book/limit.cpp
        src/book/order.cpp
        src/book/orderbook.cpp
        src/book/order_pool.cpp
        src/strategies/imbalance_strat.cpp
        src/async_logger.cpp
        src/backtester.cpp
        src/main.cpp
        src/parser.cpp
        include/qcustomplot/qcustomplot.cpp
        src/concurrent_backtest.cpp

)

set(HEADERS
        ${HEADERS}
        include/lock_free_queue.h
        include/message.h
        include/strategy.h
        include/qcustomplot/qcustomplot.h
        src/strategies/linear_model_strat.cpp
        include/lookup_table/lookup_table.h
        include/lookup_table/xxhash/xxhash.h
        include/threadpool.h
        src/connection_pool.cpp
        src/db_connection.cpp
        include/connection_pool.h
        include/db_connection.h
        include/backtester.h
        include/concurrent_backtest.h
        include/async_logger.h
)

find_package(AWSSDK REQUIRED COMPONENTS s3)
find_package(Boost 1.75.0 REQUIRED COMPONENTS system filesystem)
find_library(PQXX_LIB pqxx PATHS ${PQXX_LIBRARY_DIR} REQUIRED)
find_library(CURL_LIB curl PATHS ${CURL_LIBRARY_DIR} REQUIRED)
find_library(XXHASH_LIB xxhash PATHS ${XXHASH_LIBRARY_DIR} REQUIRED)

find_package(Qt6 COMPONENTS Core Gui Widgets Charts PrintSupport REQUIRED)

include_directories(
        ${CMAKE_CURRENT_SOURCE_DIR}/include
        ${Boost_INCLUDE_DIRS}
        ${PQXX_INCLUDE_DIR}
        ${JSON_INCLUDE_DIR}
        ${CURL_INCLUDE_DIR}
        ${WEBSOCKETPP_INCLUDE_DIR}
        ${EIGEN_INCLUDE_DIR}
        ${AWS_SDK_PATH}/include
        ${XXHASH_INCLUDE_DIR}
)

add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})

target_link_libraries(${PROJECT_NAME} PRIVATE
        ${AWSSDK_LIBRARIES}
        aws-cpp-sdk-core
        aws-cpp-sdk-s3
        Boost::boost
        ${PQXX_LIB}
        ${CURL_LIB}
        Qt6::Core
        Qt6::Gui
        Qt6::Widgets
        Qt6::Charts
        Qt6::PrintSupport
        ${XXHASH_LIB}
        databento::databento
)

target_include_directories(${PROJECT_NAME} PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/include
        ${Boost_INCLUDE_DIRS}
        ${PQXX_INCLUDE_DIR}
        ${JSON_INCLUDE_DIR}
        ${CURL_INCLUDE_DIR}
        ${WEBSOCKETPP_INCLUDE_DIR}
        ${EIGEN_INCLUDE_DIR}
        ${AWS_SDK_PATH}/include
)

link_directories(
        ${AWS_SDK_PATH}/lib
)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pg")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -pg")

set(CMAKE_BUILD_RPATH ${CMAKE_BUILD_RPATH} ${AWS_SDK_PATH}/lib)
set(CMAKE_INSTALL_RPATH ${CMAKE_INSTALL_RPATH} ${AWS_SDK_PATH}/lib)

set_target_properties(${PROJECT_NAME} PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin
)

```

## High-Level Overview

This is a CMake build configuration file.

**Project Name**: `databento_orderbook`

**Dependencies:**
- AWSSDK
- Boost
- Qt6

**Executables Defined:**
- ${PROJECT_NAME}



## Detailed Code Walkthrough

This file contains configuration or implementation details. See the source code above for specifics.


## Usage Examples

To build the project:

```bash
mkdir build
cd build
cmake ..
make
```


## Performance & Security Notes

No specific performance or security concerns identified. Follow standard C++ best practices.


## Related Files

No directly related files identified.


## Testing

To test this component:

1. Build the project using CMake
2. Run the executable
3. Verify expected behavior

Consider adding unit tests for critical functions.
