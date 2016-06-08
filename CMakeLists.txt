cmake_minimum_required(VERSION 2.8.11 FATAL_ERROR)
project(BoostCMake NONE)

enable_testing()

message(STATUS "Using CMake ${CMAKE_VERSION}")
list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/Modules")

if(NOT EXISTS "${PROJECT_SOURCE_DIR}/Frameworks/boost")
    find_package(Git REQUIRED)
    execute_process(COMMAND
        ${CMAKE_SOURCE_DIR}/shallow-clone develop
        https://github.com/boostorg/boost.git
        ${CMAKE_BINARY_DIR}/Frameworks/boost
        )
endif()

if(NOT EXISTS "${CMAKE_BINARY_DIR}/Frameworks/boost")
    file(COPY "Frameworks/boost" DESTINATION "${CMAKE_BINARY_DIR}/Frameworks")
endif()

file(COPY "Sources/" DESTINATION "${CMAKE_BINARY_DIR}/Frameworks/boost")

add_subdirectory("${CMAKE_BINARY_DIR}/Frameworks/boost" "${CMAKE_BINARY_DIR}/boost-build")