# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
cmake_policy(SET CMP0042 NEW)

project(BoostProgramOptions CXX)

list(APPEND CURRENT_TARGET_SOURCES
    src/cmdline
    src/config_file
    src/options_description
    src/parsers
    src/variables_map
    src/value_semantic
    src/positional_options
    src/utf8_codecvt_facet
    src/convert
    src/winmain
    src/split
    )

if(BUILD_SHARED_LIBRARIES)
    add_library(boost_program_options SHARED
        ${CURRENT_TARGET_SOURCES}
        )
else()
    add_library(boost_program_options STATIC
        ${CURRENT_TARGET_SOURCES}
        )
endif()

target_include_directories(boost_program_options PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
    )

target_link_libraries(boost_program_options PRIVATE
    boost::assert
    boost::any
    boost::math::special_functions
    boost::detail
    boost::config
    boost::concept_check
    boost::conversion
    boost::function
    boost::lexical_cast
    boost::tokenizer
    boost::type_index
    )

add_library(boost::program_options ALIAS boost_program_options)
set_property(TARGET boost_program_options PROPERTY EXPORT_NAME program_options)

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_program_options EXPORT ${PROJECT_NAME}Targets
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

export(TARGETS
        NAMESPACE boost::
        FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)
