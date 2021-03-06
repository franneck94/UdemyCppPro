cmake_minimum_required(VERSION 3.14)

set(PROJECT "CommandLineArguments")

project("${PROJECT}_Project" VERSION 1.2.3 LANGUAGES CXX)

# Global CMake variables are set here
set(CMAKE_CXX_STANDARD          17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS        OFF)

# Options
option(ENABLE_TESTING       "Enable a Unit Testing buid."   ON)
option(ENABLE_COVERAGE      "Enable a Code Coverage buid."  OFF)
option(ENABLE_BENCHMARKS    "Enable a Code Coverage buid."  ON)

# Cmake modules
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake/)
include(Documentation)
if (ENABLE_TESTING OR ENABLE_COVERAGE)
    include(CTest)
endif()
if (ENABLE_COVERAGE)
    if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
        include(CodeCoverage)
    else()
        message("Coverage only for GNU|Clang")
        set(ENABLE_COVERAGE OFF)
    endif()
endif()

# Project/Library Names
set(LIBRARY_NAME    "${PROJECT}_MyLib")
set(MAIN            "${PROJECT}_Executable")
set(TEST_MAIN       "${PROJECT}_unit_tests")
set(COVERAGE_MAIN   "${PROJECT}_coverage")
set(BENCHMARK_MAIN  "${PROJECT}_benchmarks")

# Add conan libraries
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

# Add external libraries
add_subdirectory("${PROJECT_SOURCE_DIR}/external/")

# Add first-party libraries
add_subdirectory("${PROJECT_SOURCE_DIR}/src/")

# Add executables
add_subdirectory("${PROJECT_SOURCE_DIR}/app/")

# Add unit tests
if (ENABLE_TESTING OR ENABLE_COVERAGE)
    enable_testing()
    add_subdirectory("${PROJECT_SOURCE_DIR}/tests/")
endif()

# Add benchmarks
if(ENABLE_BENCHMARKING)
    add_subdirectory("${PROJECT_SOURCE_DIR}/benchmarks/")
endif()
