# Distributed under the Boost Software License, Version 1.0.
# See http://www.boost.org/LICENSE_1_0.txt

cmake_minimum_required(VERSION 2.8.11)
cmake_policy(SET CMP0042 NEW)

project(BoostWave CXX)

if(MSVC)
    add_definitions(
            -D_CRT_SECURE_NO_WARNINGS
            -D_SCL_SECURE_NO_WARNINGS
    )
endif()

list(APPEND CURRENT_TARGET_SOURCES
        src/instantiate_cpp_exprgrammar.cpp
        src/instantiate_cpp_grammar.cpp
        src/instantiate_cpp_literalgrs.cpp
        src/instantiate_defined_grammar.cpp
        src/instantiate_predef_macros.cpp
        src/instantiate_re2c_lexer.cpp
        src/instantiate_re2c_lexer_str.cpp
        src/token_ids.cpp
        src/wave_config_constant.cpp
        src/cpplexer/re2clex/aq.cpp
        src/cpplexer/re2clex/cpp_re.cpp
        )

if(BUILD_SHARED_LIBRARIES)
    add_library(boost_wave SHARED
            ${CURRENT_TARGET_SOURCES}
            )
else()
    add_library(boost_wave STATIC
            ${CURRENT_TARGET_SOURCES}
            )
endif()

target_include_directories(boost_wave PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        )

target_link_libraries(boost_wave
        boost::assert
        boost::thread
        boost::spirit
        boost::filesystem
        boost::multi_index
        boost::pool
        )

add_library(boost::wave ALIAS boost_wave)
set_property(TARGET boost_wave PROPERTY EXPORT_NAME wave)

if(${CMAKE_TARGET_ARCHITECTURE} STREQUAL ${CMAKE_HOST_SYSTEM_PROCESSOR})
    add_executable(wave
            tool/cpp.cpp
            )

    target_link_libraries(wave
            boost::wave
            boost::timer
            boost::any
            boost::serialization
            boost::foreach
            boost::program_options
            )

    install(TARGETS wave
            EXPORT ${PROJECT_NAME}Targets
            RUNTIME DESTINATION bin
            LIBRARY DESTINATION lib
            ARCHIVE DESTINATION lib
            )

    export(TARGETS wave
            NAMESPACE boost::
            FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)
endif()

install(DIRECTORY include/ DESTINATION include)

install(TARGETS boost_wave
        EXPORT ${PROJECT_NAME}Targets
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

export(TARGETS boost_wave
        NAMESPACE boost::
        FILE ${CMAKE_BINARY_DIR}/lib/cmake/${PROJECT_NAME}/${PROJECT_NAME}Targets.cmake)

if(APPLE AND NOT ${CMAKE_TARGET_ARCHITECTURE} STREQUAL ${CMAKE_HOST_SYSTEM_PROCESSOR})
    set_target_properties(boost_wave PROPERTIES XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY ${APPLE_CODE_SIGN_IDENTITY})
    set_target_properties(boost_wave PROPERTIES XCODE_ATTRIBUTE_DEVELOPMENT_TEAM ${CMAKE_XCODE_ATTRIBUTE_DEVELOPMENT_TEAM})
endif()