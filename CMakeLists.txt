cmake_minimum_required(VERSION 2.8.11 FATAL_ERROR)
cmake_policy(SET CMP0042 NEW)

project(BoostCMake NONE)

list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/Modules")

include(TargetArchitecture)

option(BUILD_UNIT_TESTS "Build unit tests" FALSE)
option(BUILD_SHARED_LIBRARIES "Build shared libraries" TRUE)

set(APPLE_CODE_SIGN_IDENTITY "" CACHE STRING "Sign identity name")
set(CMAKE_TARGET_ARCHITECTURE "" CACHE string "Target build architecture")

if(NOT CMAKE_TARGET_ARCHITECTURE)
    target_architecture(CMAKE_TARGET_ARCHITECTURE)
endif()

if(APPLE)
    set(CMAKE_OSX_ARCHITECTURES ${CMAKE_TARGET_ARCHITECTURE})
endif()

if(BUILD_UNIT_TESTS)
    enable_testing()
endif()

if(NOT EXISTS "${PROJECT_SOURCE_DIR}/frameworks/boost")
    find_package(Git REQUIRED)
    execute_process(COMMAND
            ${CMAKE_SOURCE_DIR}/shallow-clone develop
            https://github.com/boostorg/boost.git
            ${CMAKE_BINARY_DIR}/frameworks/boost
            )
endif()

if(NOT EXISTS "${CMAKE_BINARY_DIR}/frameworks/boost")
    file(COPY "frameworks/boost" DESTINATION "${CMAKE_BINARY_DIR}/frameworks")
endif()

file(COPY "src/" DESTINATION "${CMAKE_BINARY_DIR}/frameworks/boost")

add_subdirectory("${CMAKE_BINARY_DIR}/frameworks/boost"
        "${CMAKE_BINARY_DIR}/frameworks/boost")
