# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
project(BoostCore)

add_library(boost_core INTERFACE)
add_library(boost::core ALIAS boost_core)
set_property(TARGET boost_core PROPERTY EXPORT_NAME core)

target_include_directories(boost_core INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    )

target_link_libraries(boost_core INTERFACE
    boost::assert
    boost::config
    boost::static_assert
    boost::predef
    )

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_core EXPORT ${PROJECT_NAME}Targets
    INCLUDES DESTINATION include
    )

install(EXPORT ${PROJECT_NAME}Targets
    FILE ${PROJECT_NAME}Targets.cmake
    NAMESPACE boost::
    DESTINATION lib/cmake/${PROJECT_NAME}
    )

write_config_file("${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake"
    Assert
    Config
    StaticAssert
    Predef
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

export(TARGETS boost_core
    NAMESPACE boost::
    FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)
