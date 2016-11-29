# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
cmake_policy(SET CMP0042 NEW)

project(BoostChrono CXX)

if(CMAKE_SYSTEM MATCHES "Linux")
    find_library(RT_LIBRARY rt)
endif()

list(APPEND CURRENT_TARGET_SOURCES
    src/chrono.cpp
    src/thread_clock.cpp
    src/process_cpu_clocks.cpp
    )

if(BUILD_SHARED_LIBRARIES)
    add_library(boost_chrono SHARED
        ${CURRENT_TARGET_SOURCES}
        )
else()
    add_library(boost_chrono STATIC
        ${CURRENT_TARGET_SOURCES}
        )
endif()

target_include_directories(boost_chrono PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
    )

target_link_libraries(boost_chrono PUBLIC
    boost::assert
    boost::exception
    boost::math::special_functions
    boost::system
    boost::type_traits
    boost::ratio
    ${RT_LIBRARY}
    )

add_library(boost::chrono ALIAS boost_chrono)
set_property(TARGET boost_chrono PROPERTY EXPORT_NAME chrono)

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_chrono EXPORT ${PROJECT_NAME}Targets
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    )

install(EXPORT ${PROJECT_NAME}Targets
    FILE ${PROJECT_NAME}Targets.cmake
    NAMESPACE boost::
    DESTINATION lib/cmake/${PROJECT_NAME}
    )

write_config_file("${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake"
    Exception
    Math
    System
    TypeTraits
    Ratio
    )
write_basic_package_version_file("${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}ConfigVersion.cmake"
    VERSION ${BOOST_VERSION}
    COMPATIBILITY AnyNewerVersion
    )

install(FILES
    "${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake"
    "${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}ConfigVersion.cmake"
    DESTINATION lib/cmake/${PROJECT_NAME}
    )

export(TARGETS boost_chrono
    NAMESPACE boost::
    FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)

if(APPLE AND NOT ${CMAKE_TARGET_ARCHITECTURE} STREQUAL ${CMAKE_HOST_SYSTEM_PROCESSOR})
    set_target_properties(boost_chrono PROPERTIES XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY ${APPLE_CODE_SIGN_IDENTITY})
    set_target_properties(boost_chrono PROPERTIES XCODE_ATTRIBUTE_DEVELOPMENT_TEAM ${CMAKE_XCODE_ATTRIBUTE_DEVELOPMENT_TEAM})
endif()