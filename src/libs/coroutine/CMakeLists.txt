# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
cmake_policy(SET CMP0042 NEW)

project(BoostCoroutine CXX)

if(BUILD_UNIT_TESTS)
    add_subdirectory(test)
endif()

if(WIN32)
    set(allocator_sources
        src/detail/standard_stack_allocator_windows.cpp
        )
else()
    set(allocator_sources
        src/detail/standard_stack_allocator_posix.cpp
        )
    if(CMAKE_COMPILER_IS_GNUCXX AND "${CMAKE_CXX_COMPILER_VERSION}" VERSION_GREATER "4.7")
        list(APPEND allocator_sources
            src/detail/segmented_stack_allocator.cpp
            )
        add_definitions(-DBOOST_USE_SEGMENTED_STACKS)
    endif()
endif()

list(APPEND CURRENT_TARGET_SOURCES
    ${allocator_sources}
    src/detail/coroutine_context.cpp
    src/exceptions.cpp
    )

if(BUILD_SHARED_LIBRARIES)
    add_library(boost_coroutine SHARED
        ${CURRENT_TARGET_SOURCES}
        )

else()
    add_library(boost_coroutine STATIC
        ${CURRENT_TARGET_SOURCES}
        )

endif()

target_include_directories(boost_coroutine PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
    )

target_link_libraries(boost_coroutine PUBLIC
    boost::assert
    boost::context
    boost::system
    boost::type_traits
    )

add_library(boost::coroutine ALIAS boost_coroutine)
set_property(TARGET boost_coroutine PROPERTY EXPORT_NAME coroutine)

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_coroutine EXPORT ${PROJECT_NAME}Targets
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
    Context
    System
    TypeTraits
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

export(TARGETS boost_coroutine
    NAMESPACE boost::
    FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)

if(APPLE AND NOT ${CMAKE_TARGET_ARCHITECTURE} STREQUAL ${CMAKE_HOST_SYSTEM_PROCESSOR})
    set_target_properties(boost_coroutine PROPERTIES XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY ${APPLE_CODE_SIGN_IDENTITY})
    set_target_properties(boost_coroutine PROPERTIES XCODE_ATTRIBUTE_DEVELOPMENT_TEAM ${CMAKE_XCODE_ATTRIBUTE_DEVELOPMENT_TEAM})
endif()