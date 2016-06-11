# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
cmake_policy(SET CMP0042 NEW)

project(BoostFilesystem CXX)

list(APPEND CURRENT_TARGET_SOURCES
    src/operations.cpp
    src/path.cpp
    src/portability.cpp
    src/utf8_codecvt_facet.cpp
    src/codecvt_error_category.cpp
    src/path_traits.cpp
    src/unique_path.cpp
    src/windows_file_codecvt.cpp        
    )

if(BUILD_SHARED_LIBRARIES)
    add_library(boost_filesystem SHARED
        ${CURRENT_TARGET_SOURCES}
        )
else()
    add_library(boost_filesystem STATIC
        ${CURRENT_TARGET_SOURCES}
        )
endif()

target_include_directories(boost_filesystem PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    )

target_link_libraries(boost_filesystem PUBLIC
    boost::assert
    boost::system
    boost::exception
    boost::functional
    boost::type_traits
    boost::range
    boost::io
    boost::smart_ptr
    )

add_library(boost::filesystem ALIAS boost_filesystem)
set_property(TARGET boost_filesystem PROPERTY EXPORT_NAME filesystem)

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_filesystem EXPORT ${PROJECT_NAME}Targets
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
    System
    Exception
    Functional
    TypeTraits
    Range
    IO
    SmartPtr
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

export(TARGETS boost_filesystem
    NAMESPACE boost::
    FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)
