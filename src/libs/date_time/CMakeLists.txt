# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
cmake_policy(SET CMP0042 NEW)

project(BoostDateTime)

add_definitions(
    -DDATE_TIME_INLINE
    )

list(APPEND CURRENT_TARGET_SOURCES
    src/gregorian/greg_month.cpp
    src/gregorian/greg_weekday.cpp
    src/gregorian/date_generators.cpp
    src/gregorian/gregorian_types.cpp
    )

if(BUILD_SHARED_LIBRARIES)
    add_library(boost_date_time SHARED
        ${CURRENT_TARGET_SOURCES}
        )
else()
    add_library(boost_date_time STATIC
        ${CURRENT_TARGET_SOURCES}
        )
endif()

target_include_directories(boost_date_time PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
    )

target_link_libraries(boost_date_time PRIVATE
    boost::assert
    boost::config
    boost::conversion
    boost::concept_check
    boost::detail
    boost::math::special_functions
    boost::lexical_cast
    boost::type_traits
    boost::integer
    boost::smart_ptr
    boost::tokenizer
    )

add_library(boost::date_time ALIAS boost_date_time)
set_property(TARGET boost_date_time PROPERTY EXPORT_NAME date_time)

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_date_time EXPORT ${PROJECT_NAME}Targets
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    )

install(EXPORT ${PROJECT_NAME}Targets
    FILE ${PROJECT_NAME}Targets.cmake
    NAMESPACE boost::
    DESTINATION lib/cmake/${PROJECT_NAME}
    )

write_config_file("${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake")
write_basic_package_version_file("${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}ConfigVersion.cmake"
    VERSION ${BOOST_VERSION}
    COMPATIBILITY AnyNewerVersion
    )

install(FILES
    "${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Config.cmake"
    "${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}ConfigVersion.cmake"
    DESTINATION lib/cmake/${PROJECT_NAME}
    )

export(TARGETS boost_date_time
    NAMESPACE boost::
    FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)

if(APPLE AND NOT ${CMAKE_TARGET_ARCHITECTURE} STREQUAL ${CMAKE_HOST_SYSTEM_PROCESSOR})
    set_target_properties(boost_date_time PROPERTIES XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY ${APPLE_CODE_SIGN_IDENTITY})
    set_target_properties(boost_date_time PROPERTIES XCODE_ATTRIBUTE_DEVELOPMENT_TEAM ${CMAKE_XCODE_ATTRIBUTE_DEVELOPMENT_TEAM})
endif()
